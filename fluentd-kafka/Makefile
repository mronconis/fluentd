
IMAGE_NAME=fluentd-kafka:latest
CONTAINER_NAME=fluentd-kafka-docker
FLUENTD_CONF=fluentd.conf

build:
	docker build -t ${IMAGE_NAME} .

run:
	docker run -it --rm --name ${CONTAINER_NAME} \
		-v ${PWD}:/fluentd/etc \
		-v ${PWD}/test:/logs:rw \
		-e FLUENTD_CONF=${FLUENTD_CONF} \
		${IMAGE_NAME}