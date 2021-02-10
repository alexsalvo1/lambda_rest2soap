<!-- GETTING STARTED -->
## Getting Started

This is a procedure on how to create a container image, starting from a simple nodejs application!

# lambda_rest2soap
This is a sample lambda function (Node.js implementation) that when coupled with AWS API Gateway, could help to transform SOAP service calls into REST APIs.
The SOAP service used for this sample is available @ http://www.dneonline.com/calculator.asmx?wsdl

### Prerequisites

* Initialization
    ```sh
    cd lambda_rest2soap
    npm init
    npm install soap
    ```

### Container Image

1. Create a local container image
   ```sh
   docker build -t lambda_rest2soap-container .
   ```
2. Run Container locally on a different terminal 
   ```sh
   docker run -p 9000:8080 lambda_rest2soap-container:latest
    ```
3. Test container on previous terminal
    ```sh
    curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{"queryStringParameters":{"a":10,"b":20}}'
    ```
4. Create my ECR repository
    ```sh
    registryId=$(aws ecr create-repository --repository-name lambda_rest2soap --image-scanning-configuration scanOnPush=true)
    ```
5. If repository already exist
    ```sh
    registryId=$(aws ecr describe-registry --query [registryId] --output text)
    ```
6. Push local image to my ECR
    ```sh
    docker tag lambda_rest2soap-container:latest ${registryId}.dkr.ecr.eu-west-1.amazonaws.com/lambda_rest2soap:latest
    aws ecr get-login-password | docker login --username AWS --password-stdin $registryId.dkr.ecr.eu-west-1.amazonaws.com
    docker push $registryId.dkr.ecr.eu-west-1.amazonaws.com/lambda_rest2soap:latest
    ```

