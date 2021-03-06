# Pushing Windows Images to Amazon ECR<a name="windows_ecr"></a>

You can push Windows Docker container images to Amazon ECR\. You must be using a version of Docker that supports Windows containers\. The following procedures show you how to pull a Windows Docker image, create an Amazon ECR repository to store the image, tag the image to that repository, authenticate the image to the Amazon ECR registry, and then push the image to that repository\.

**To pull and tag a Windows Docker image**

1. Pull a Windows Docker image locally\. This example uses the `microsoft/iis` image\.

   ```
   PS C:\> docker pull microsoft/iis
   Using default tag: latest
   latest: Pulling from microsoft/iis
   
   3889bb8d808b: Pull complete
   04ee5d718c7a: Pull complete
   c0931dd15237: Pull complete
   61784b745c20: Pull complete
   d05122f129ca: Pull complete
   Digest: sha256:25586570b058da9882d4af640d326d0cc26dfd60b67e1cee63f35ea54d83c882
   Status: Downloaded newer image for microsoft/iis:latest
   ```

1. Create an Amazon ECR repository for your image\.

   ```
   PS C:\> aws ecr create-repository --repository-name iis
   {
       "repository": {
           "registryId": "111122223333",
           "repositoryName": "iis",
           "repositoryArn": "arn:aws:ecr:us-west-2:111122223333:repository/iis",
           "createdAt": 1481845593.0,
           "repositoryUri": "111122223333.dkr.ecr.us-west-2.amazonaws.com/iis"
       }
   }
   ```

1. Tag the image with the `repositoryUri` that was returned from the previous command\.

   ```
   PS C:\> docker tag microsoft/iis 111122223333.dkr.ecr.us-west-2.amazonaws.com/iis
   ```

1. Authenticate your Docker client to the Amazon ECR registry\.
**Note**  
The get\-login command is available in the AWS CLI starting with version 1\.9\.15; however, we recommend version 1\.11\.91 or later for recent versions of Docker \(17\.06 or later\)\. You can check your AWS CLI version with the aws \-\-version command\. If you are using Docker version 17\.06 or later, include the `--no-include-email` option after `get-login`\. If you receive an `Unknown options: --no-include-email` error, install the latest version of the AWS CLI\. For more information, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.

   ```
   PS C:\> Invoke-Expression -Command (aws ecr get-login)
   ```

1. Push the image to Amazon ECR\.

   ```
   PS C:\> docker push 111122223333.dkr.ecr.us-west-2.amazonaws.com/iis
   The push refers to a repository [111122223333.dkr.ecr.us-west-2.amazonaws.com/iis]
   1e4f77a75bd4: Pushed
   ac90fb7da567: Pushed
   c7090349c7b3: Pushed
   b9454c3094c6: Skipped foreign layer
   3fd27ecef6a3: Skipped foreign layer
   latest: digest: sha256:0ddc7af8691072bb2dd8b3f189388b33604c90774d3dc0485b1bf379f9bec4c5 size: 1574
   ```