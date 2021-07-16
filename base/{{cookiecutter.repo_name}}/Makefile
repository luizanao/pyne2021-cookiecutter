# Makefile for {{cookiecutter.app_name}} Service

# VARIABLES

# Change these variables.
service := {{cookiecutter.container_name}}
tag := $(or ${BUILD_NUMBER},dev)

python_version := 3.6
pip_version := 18.1
black_version := 18.9b0

# TARGETS

.PHONY: test style run

test:
	pytest tests

style:
	black .

run:
	python -m {{cookiecutter.app_name}}.main
