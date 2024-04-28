# simple-CICD

Runs on self-hosted



name: Python-greetings

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  workflow_dispatch:

jobs:
  install-pip-deps:
    name: install-pip-deps
    runs-on: self-hosted
    steps:
      - name: ... Clone Repository ...
        uses: actions/checkout@v3.5.2
        with:
          repository: ReinisVelmeris/python-greetings
          ref: main
          path: ./source
      - name: ... Install dependencies ...
        working-directory: ./source/python-greetings
        run: pip3 install -r requirements.txt
    
  deploy-to-dev:
    name: deploy-to-dev
    runs-on: self-hosted
    needs: [install-pip-deps]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/deploy
        with:
          port: 7001
          enviroment: 'dev'

  tests-on-dev:
    name: tests-on-dev
    runs-on: self-hosted
    needs: [deploy-to-dev]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/test
        with:
          enviroment: 'dev'

  deploy-to-staging:
    name: deploy-to-staging
    runs-on: self-hosted
    needs: [tests-on-dev]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/deploy
        with:
          port: 7002
          enviroment: 'staging'   

  tests-on-staging:
    name: Tests
    runs-on: self-hosted
    needs: [deploy-to-staging]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/test
        with:
          enviroment: 'staging'

  deploy-to-preprod:
    name: deploy-to-preprod
    runs-on: self-hosted
    needs: [tests-on-staging]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/deploy
        with:
          port: 7003
          enviroment: 'preprod'     

  tests-on-preprod:
    name: tests-on-preprod
    runs-on: self-hosted
    needs: [deploy-to-preprod]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/test
        with:
          enviroment: 'preprod'

  deploy-to-prod:
    name: deploy-to-prod
    runs-on: self-hosted
    needs: [tests-on-preprod]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/deploy
        with:
          port: 7004
          enviroment: 'prod'   
  
  tests-on-prod:
    name: tests-on-prod
    runs-on: self-hosted
    needs: [deploy-to-prod]
    steps:
      - uses: actions/checkout@v3.5.2
      - uses: ./.github/actions/test
        with:
          enviroment: 'prod'