
IMAGE_NAME=custom-fluentd:latest
CONTAINER_NAME=custom-fluentd-docker
FLUENTD_CONF=fluentd.conf
EXPOSE_PORT=9880

build:
	docker build -t ${IMAGE_NAME} .

run:
	docker run -it --rm --name ${CONTAINER_NAME} \
		-p ${EXPOSE_PORT}:9880 \
		-v ${PWD}:/fluentd/etc \
		-e FLUENTD_CONF=${FLUENTD_CONF} \
		${IMAGE_NAME}