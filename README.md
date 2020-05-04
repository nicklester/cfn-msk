# cfn-modules: AWS MSK

AWS MSK (Kafka Cluster) using two or three availability zones with public and private subnets. Cloudwatch logging enabled by default.

## Limitations

* At present only CloudWatch logging is not enabled, nor is logging through Prometheus, but this is expected to be added in a subsequent version

## Install


## Usage

```
---
AWSTemplateFormatVersion: '2010-09-09'
Parameters: 
  VpcStackName: 
    Type: String
Resources:
  MskClientSg:
    Type: 'AWS::CloudFormation::Stack'
    Properties:
      Parameters:
        VpcModule: !Ref VpcStackName
      TemplateURL: './node_modules/@cfn-modules/client-sg/module.yml'
  Msk:
    Type: AWS::CloudFormation::Stack
    Properties:
      Parameters:
        BrokerCount: 2
        ClientSgModule: !GetAtt 'MskClientSg.Outputs.StackName'
        VpcModule: !Ref VpcStackName
        InstanceType: kafka.t3.small
      TemplateURL: 'node_modules/cfn-msk/module.yml'
```

Once the stack has created you can obtain the Bootstrap Brokers by calling the AWS API:

https://docs.aws.amazon.com/msk/latest/developerguide/msk-get-bootstrap-brokers.html

e.g.

* To obtain the ARN: 
```$(aws2 cloudformation describe-stacks --stack-name <Stack Name> --query 'Stacks[0].Outputs[?OutputKey==`ClusterName`].OutputValue' --output text)```

* To obtain the Bootstrap Brokers:
```aws2 kafka get-bootstrap-brokers --cluster-arn <ARN obtained above>```


## Examples

No examples supplied

## Parameters

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
      <th>Default</th>
      <th>Required?</th>
      <th>Allowed values</th>
    </tr>
  </thead>
  <tbody>
		<tr>
		  <td>VpcModule</td>
		  <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/vpc">vpc module</a></td>
		  <td></td>
		  <td>true</td>
		  <td></td>
		</tr>
		<tr>
	      <td>ClientSgModule</td>
	      <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/client-sg">client-sg module</a> where traffic is allowed from on port 9092, 9095 and 2181 to the cluster</td>
	      <td></td>
	      <td>yes</td>
	      <td></td>
	    </tr>
	    <tr>
		  <td>Use3Subnets</td>
		  <td>If using a region/VPC with three private subnets you can set this to true</td>
		  <td>false</td>
		  <td>false</td>
		  <td>false, true</td>
		</tr>
		<tr>
		  <td>BrokerCount</td>
		  <td>Number of Kafka Brokers to launch. Note this must be a multiple of number of subnets supplied below.</a></td>
		  <td>2</td>
		  <td>true</td>
		  <td>A multiple of subnet counts (2/3)</td>
		</tr>
		<tr>
		  <td>InstanceType</td>
		  <td>Aws Msk <a href="https://aws.amazon.com/msk/pricing/">Instance type</a> to launch</td>
		  <td>kafka.t3.small</td>
		  <td>true</td>
		  <td></td>
		</tr>
		<tr>
		  <td>MskConfigurationArn</td>
		  <td>Arn referencing specific <a href="https://docs.aws.amazon.com/msk/latest/developerguide/msk-configuration-operations.html">MskConfiguration</a></td>
		  <td></td>
		  <td>false</td>
		  <td></td>
		</tr>
		<tr>
		  <td>MskConfigurationNumber</td>
		  <td>Msk Configuration version number</td>
		  <td></td>
		  <td>false</td>
		  <td></td>
		</tr>
		<tr>
		  <td>TlsClientAuthentication</td>
		  <td>Enable Tls Client Authentication with broker</td>
		  <td>0</td>
		  <td>false</td>
		  <td>1,0</td>
		</tr>
		<tr>
		  <td>TlsArnList</td>
		  <td>Tls Certificate Arn List. Requires Tlc Client Authentication to be 1/td>
		  <td></td>
		  <td>false</td>
		  <td></td>
		</tr>
		<tr>
		  <td>ClientBrokerEncryption</td>
		  <td>Congigure client/broker data in transit encryption</td>
		  <td>TLS_PLAINTEXT</td>
		  <td>false</td>
		  <td>TLS, PLAINTEXT, TLS_PLAINTEXT</td>
		</tr>
		<tr>
		  <td>LogsRetentionInDays</td>
		  <td>Number of days to retain Cloudwatch logs</td>
		  <td>14</td>
		  <td>false</td>
		  <td>1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653</td>
		</tr>
	</tbody>
</table>