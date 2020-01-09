BUCKET_NAME ?= default
BUCKET_ACL ?= private

aws := aws --endpoint=$(ENDPOINT)

# for minio we rely only on AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
export AWS_ACCESS_KEY_ID     ?= $(error Missing AWS_ACCESS_KEY_ID)
export AWS_SECRET_ACCESS_KEY ?= $(error Missing AWS_SECRET_ACCESS_KEY)
unexport AWS_CA_BUNDLE
unexport AWS_DEFAULT_PROFILE
unexport AWS_PROFILE
unexport AWS_ROLE_SESSION_NAME
unexport AWS_SESSION_TOKEN

test-connect:
	@ echo "Checking connectivity to: $(ENDPOINT)"
	$(aws) s3 ls

deploy: test-connect
	$(aws) s3api head-bucket --bucket=$(BUCKET_NAME) || \
		$(aws) s3api create-bucket \
			--bucket=$(BUCKET_NAME) \
			--acl=$(BUCKET_ACL)

undeploy:
	- $(aws) s3api delete-bucket --bucket=$(BUCKET)
