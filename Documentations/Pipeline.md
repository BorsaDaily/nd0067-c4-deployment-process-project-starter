# Pipeline process

implementing Pipeline Process using Circle Ci

1.  config.yml

```
version: 2.1
orbs:
  aws-cli: circleci/aws-cli@2.0.3
  aws-s3: circleci/aws-s3@3.0.0
  aws-elastic-beanstalk: circleci/aws-elastic-beanstalk@2.0.1
  node: circleci/node@5.0.2
  browser-tools: circleci/browser-tools@1.3.0
jobs:
  build:
    docker:
      - image: cimg/base:stable
    steps:
      - node/install
      - checkout
      - aws-cli/setup
      - aws-elastic-beanstalk/setup
      - browser-tools/install-chrome
      - browser-tools/install-chromedriver
      - aws-cli/setup:
          aws-access-key-id: AWS_ACCESS_KEY_ID 
          aws-secret-access-key: AWS_SECRET_ACCESS_KEY 
          aws-region: AWS_REGION
      - run:
          name: install angular cli globally
          command: |
           npm install -g @angular/cli
      - run:
          name: Front-End Install
          command: |
            cd ./udagram/udagram-frontend && npm install 
      - run:
          name: Back-End Install
          command: |
            cd ./udagram/udagram-api && npm install          
      - run:
          name: Front-End build
          command: |
            cd ./udagram/udagram-frontend && npm run build 
      - run:
          name: Back-End build
          command: |
            cd ./udagram/udagram-api && npm run build 
      - run:
          name: Front-End test
          command: |
            google-chrome --version
            chromedriver --version
            cd ./udagram/udagram-frontend && npm run test
      - run:
          name: Front-End deploy
          command: |
            cd ./udagram/udagram-frontend && npm run deploy     
      - run:
          name: get itdentity  
          command: | 
            aws sts get-caller-identity   
      - run:
          name: EB intitialization
          command: |
            eb init udagram22appR3 --platform Node.js --region us-east-1
            eb use Udagram22appr3-env
            eb status   
 
      - run:
          name: Back-End deploy
          command: |
            cd ./udagram/udagram-api
            eb init udagram22appR3 --platform Node.js --region us-east-1
            eb use Udagram22appr3-env
            npm run deploy
              
```
2. connected Github repo

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-%20linked%20Github.PNG "linked Github")

3. imported ENV Variables to CircleCi

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-%20linked%20env%20variables.PNG "ENV Variables")

4. Pipeline Work Flow Status

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-CI-CD%20status%20-1.PNG "status1")
![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-CI-CD%20status%20-2.PNG "status2")