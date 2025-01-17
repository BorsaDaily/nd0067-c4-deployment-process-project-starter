# Pipeline process

implementing Pipeline Process using Circle Ci

1.  config.yml

```
version: 2.1
orbs:
# installing Obrs to support work and testing Env.
  aws-cli: circleci/aws-cli@2.0.3
  aws-s3: circleci/aws-s3@3.0.0
  aws-elastic-beanstalk: circleci/aws-elastic-beanstalk@2.0.1
  node: circleci/node@5.0.2
  browser-tools: circleci/browser-tools@1.3.0
workflows:
#filter to Ensure that deployment for master Branch only
  buildanddeployment:
    jobs:
      - build:
          filters:
            branches:
              only: master
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
      # aws cli setup and passing admin IAM
      - aws-cli/setup:
          aws-access-key-id: AWS_ACCESS_KEY_ID
          aws-secret-access-key: AWS_SECRET_ACCESS_KEY
          aws-region: AWS_REGION
      # installing Anglular CLI    
      - run:
          name: install angular cli globally
          command: |
            npm install -g @angular/cli
      # installing Dependencies for front-End      
      - run:
          name: Front-End Install
          command: |
            npm run frontend:install
      # installing Dependencies for Back-End 
      - run:
          name: Back-End Install
          command: |
            npm run backend:install
      # Buililding script for Front-End-Build
      - run:
          name: Front-End build
          command: |
            npm run frontend:build
      # Buililding script for Back-End-Build
      - run:
          name: Back-End build
          command: |
            npm run backend:build
      # testing Front-End with Karma and chrome
      - run:
          name: Front-End test
          command: |
            google-chrome --version
            chromedriver --version
            npm run frontend:test
      # front-End Deployment script thorough CLI to S3 Bucket      
      - run:
          name: Front-End deploy
          command: |
            npm run frontend:deploy
      # Get identity of the user to ensure that IAM is working porperly    
      - run:
          name: get itdentity
          command: |
            aws sts get-caller-identity
      # Back-End Deployment script thorough CLI to EB and passing Env Variables from Circle CI   
      - run:
          name: Back-End deploy
          command: |
            eb init udagram22appR3 --platform Node.js --region us-east-1
            eb setenv AWS_BUCKET=$AWS_BUCKET AWS_PROFILE=$AWS_PROFILE AWS_REGION=$AWS_DEFAULT_REGION JWT_SECRET=$JWT_SECRET PORT=$PORT PORT_DB=$PORT_DB	 POSTGRES_DB=$POSTGRES_DB POSTGRES_HOST=$POSTGRES_HOST POSTGRES_PASSWORD=$POSTGRES_PASSWORD POSTGRES_USERNAME=$POSTGRES_USERNAME URL=$URL
            eb use Udagram22appr3-env
            npm run backend:deploy
      
```
2. connected Github repo

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-%20linked%20Github.PNG "linked Github")

3. imported ENV Variables to CircleCi

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-%20linked%20env%20variables.PNG "ENV Variables")

4. Pipeline Work Flow Status

![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-CI-CD%20status%20-1.PNG "status1")
![alt text](https://github.com/BorsaDaily/nd0067-c4-deployment-process-project-starter/blob/master/screenshots/CircleCi%20-CI-CD%20status%20-2.PNG "status2") 