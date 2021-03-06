test=''
CWD:= $(shell pwd)
HOSTNAME := $(shell hostname -i)
IMAGE=habitissimo/mariadb-replicator
DOCKER_RUN_FLAGS = --rm -e "HOST_NAME=$(HOSTNAME)"
run     : DOCKER_RUN_FLAGS += -p 5001:5001 --name=dbm-server
dev     : DOCKER_RUN_FLAGS += -p 5001:5001 -v "$(PWD):/code" -e "DEBUG=True" --name=launcher-server-dev
shell   : DOCKER_RUN_FLAGS += -it -v "$(PWD):/code" --entrypoint=ipython --name=launcher-ipython
bash    : DOCKER_RUN_FLAGS += -it -v "$(PWD):/code" --entrypoint=bash --name=launcher-bash
flake   : DOCKER_RUN_FLAGS += -it -v "$(PWD):/code" --name=launcher-flake8
tests   : DOCKER_RUN_FLAGS += -it -v "$(PWD):/code" --name=launcher-nosetest
update  : DOCKER_RUN_FLAGS += -t --name=launcher-update
DOCKER_RUN=docker run $(DOCKER_RUN_FLAGS) \
	-v /var/run/docker.sock:/var/run/docker.sock \
	-v /opt/hops:/hops \
	$(IMAGE)

# build the image
build:
	docker build -t $(IMAGE) .

# push the image to the docker registry
push:
	docker push $(IMAGE)

# run the image
run: build
	$(DOCKER_RUN)

# run the image in development mode
dev:
	$(DOCKER_RUN) python runserver.py

# run the update task (find templates and import them)
update: build
	${DOCKER_RUN} update

# check pep8
pep: flake
flake:
	${DOCKER_RUN} flake8 --ignore=E501,F841 .

# run the test suite
test: tests
tests: flake
	${DOCKER_RUN} nosetests -v -x --rednose --pdb $(test)

# open an ipython shell for testing
shell:
	${DOCKER_RUN}

# opens a shell inside the container for testing
bash:
	${DOCKER_RUN}

.PHONY: build push run dev update test tests shell
