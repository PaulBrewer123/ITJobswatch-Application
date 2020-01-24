## What are these github repos?

These repos contain the environments for application developers and infrastructure developers to work independently in their own repositories, whilst allowing the developers to use the environments that have been provisioned for them. 

The general workflow is that developers write code and need an environment to run and test it in. This environment must contain the dependencies that their software needs to run properly. They can pull this environment from the infrastructure repository when they run the command ‘berks vendor cookbooks’, as long as they:
-Have chef and berks libraries installed on their machine
-Have a berksfile containing a link to the infrastructure cookbook repository, inside the folder which they are working in

To deliver as much value as possible for a given time frame, as much of the workflow as possible is automated, the goal which is to achieve CI (Continuous Integration) and CD (Continuous Deployment). For this, the automation software ‘Jenkins’ is used. 

## Jenkins job 1 - Tests

The first Jenkins job is to run tests to ensure the code that has been pushed to the developer branch of the repository runs bug free. This includes both unit tests (testing the new code in isolation) and integration tests (testing whether the system works when then new code is added). If the tests pass, then the next Jenkins job is called. 

## What tests are running?

In the application pipeline, the first Jenkins jobs runs the pytest suite included in the application repository, which requires a slave node in Jenkins, since Python is required on any machine to run Python code on it. Therefore, an AMI is needed, to provision the slave node with Python.

In the infrastructure pipeline, the first Jenkins job runs the unit tests for the cookbooks, checking whether the syntax inside the recipe file ‘default.rb’ is correct for chef. The chef command for running the unit tests is ‘chef exec rspec’. Following the unit tests, the integration tests are run, the chef command for this being ‘kitchen test’ which builds a VM and ensures the recipe file has provisioned all the required packages and everything that the recipe is required to do is working properly. The VM is then destroyed. A slave node in Jenkins is also needed for this job since chef is required on the machine to run the tests, 
Note:
The unit tests are contained in the —- file
The integration tests are contained in the —- file

## Jenkins job 2 - Merge to master

For both repositories, if the previous job is successful then a new Jenkins job is called which merges developer code to master since it’s now known that the new code will run as desired.

Job 1 and job 2 combine to ensure the Continuous Integration (CI) part of the pipeline for both repositories.

## Job 3 - Packer

Packer is used for the automated generation of machine images, such as a vagrantfile or an Amazon AMI, which are used as templates when building virtual machines. This allows our work process to include Continuous Deployment, since the successful updating of our software leads to a new AMI being created and automatically deployed to AWS. 
