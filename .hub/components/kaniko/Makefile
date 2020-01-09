.DEFAULT_GOAL := deploy

NAMESPACE 	  ?= default

kubectl       := kubectl --context="$(DOMAIN_NAME)"
jq            := jq -M
stern         := stern --context "$(DOMAIN_NAME)" --namespace "$(NAMESPACE)" --color "never" --template "{{ .ContainerName }} {{ .Message}}"

TIMEOUT       ?= 900

namespce:
	- $(kubectl) create namespace $(NAMESPACE)

deploy: namespce
	$(kubectl) -n $(NAMESPACE) apply -f $(GIT_KIND)-secret.yaml
	$(eval job_name=$(shell $(kubectl) -n $(NAMESPACE) create -f kaniko-job.yaml -o json | $(jq) -r '.metadata.labels."job-name"'))
	@ echo "Building $(DOCKER_IMAGE). This can take a while..."
	@ $(MAKE) wait_complete JOB_NAME=$(job_name)

undeploy:
	- $(kubectl) -n $(NAMESPACE) delete -f kaniko-job.yaml
	- $(kubectl) -n $(NAMESPACE) delete -f $(GIT_KIND)-secret.yaml

wait_complete:
	$(eval timeout:=$(shell echo "`date +%s` + $(TIMEOUT)" | bc ))
	$(eval get_job=$(kubectl) -n $(NAMESPACE) get job/$(JOB_NAME) -o json )
	$(stern) --selector "job-name=$(JOB_NAME)" & echo $$! > $(JOB_NAME).pid
	@ while [ `date +%s` -le "$(timeout)" ]; do \
		if test `$(get_job) | $(jq) '.status.succeeded or .status.failed'` == 'true'; then \
			kill $$( cat $(JOB_NAME).pid ) && rm -f $(JOB_NAME).pid; \
			printf '\n-- done --\n'; \
			$(get_job) | $(jq) .status;\
			printf '\n-- eof --\n'; \
			test `$(get_job) | $(jq) '.status.succeeded' | xargs` == "1" && exit 0 || exit 1; \
		fi; \
		sleep 2; \
	done; \
	echo " ERROR timeout $(TIMEOUT)sec"; \
	kill $$( cat $(JOB_NAME).pid ) && rm -f $(JOB_NAME).pid; \
	exit 1
