[![Build Status](https://travis-ci.org/cfn-modules/msk-cluster.svg?branch=master)](https://travis-ci.org/cfn-modules/msk-cluster)
[![NPM version](https://img.shields.io/npm/v/@cfn-modules/msk-cluster.svg)](https://www.npmjs.com/package/@cfn-modules/msk-cluster)

# cfn-modules: AWS MSK cluster

AWS MSK (Kafka Cluster) using two or three availability zones with public and private subnets. Cloudwatch logging enabled by default.

## Install

> Install [Node.js and npm](https://nodejs.org/) first!

```
npm i @cfn-modules/kms-key
```

## Usage

```
---
AWSTemplateFormatVersion: '2010-09-09'
Description: 'cfn-modules example'
Resources:
  Key:
    Type: 'AWS::CloudFormation::Stack'
    Properties:
      Parameters:
        VpcModule: !GetAtt 'Vpc.Outputs.StackName' # required
        ClientSgModule: !GetAtt 'ClientSg.Outputs.StackName' # required
        KmsKeyModule: !GetAtt 'Key.Outputs.StackName' # optional
        BastionModule: !GetAtt 'Bastion.Outputs.StackName' # optional
        AlertingModule: !GetAtt 'Alerting.Outputs.StackName' # optional
        NumberOfBrokerNodes: !GetAtt 'Vpc.Outputs.NumberOfAvailabilityZones' # required
        KafkaVersion: '2.2.1' # optional
        InstanceType: 'kafka.t3.small' # optional
        MSKConfigurationArn: '' # optional
        MSKConfigurationNumber: '0' # optional
        EBSVolumeSize: '1' # optional
      TemplateURL: './node_modules/@cfn-modules/msk-cluster/module.yml'
```

## Examples

none

## Related modules

none

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
      <td>yes</td>
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
      <td>KmsKeyModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/kms-key">kms-key module</a> (only works in combination with Access := [Private, PublicRead])</td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>BastionModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/search?q=keywords:cfn-modules:Bastion">module implementing Bastion</a></td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>AlertingModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/alerting">alerting module</a></td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>NumberOfBrokerNodes</td>
      <td>The number of broker nodes you want in the Amazon MSK cluster. You can submit an update to increase the number of broker nodes in a cluster.</td>
      <td></td>
      <td>yes</td>
      <td>Has to be a multiple of the private subnets in your VPC.</td>
    </tr>
    <tr>
      <td>KafkaVersion</td>
      <td>The version of Apache Kafka.</td>
      <td>2.2.1</td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>InstanceType</td>
      <td>The type of Amazon EC2 instances to use for brokers.</td>
      <td>kafka.t3.small</td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>MSKConfigurationArn</td>
      <td>Amazon Resource Name (ARN) of the MSK configuration to use.</td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>MSKConfigurationNumber</td>
      <td>Revision of the Amazon MSK configuration to use (required if MSKConfigurationArn is set).</td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>EBSVolumeSize</td>
      <td>The size in GiB of the EBS volume for the data drive on each broker node.</td>
      <td>1</td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>ClientBrokerEncryption</td>
      <td>Indicates the encryption setting for data in transit between clients and brokers.</td>
      <td>TLS</td>
      <td>no</td>
      <td>[TLS, PLAINTEXT, TLS_PLAINTEXT]</td>
    </tr>
    <tr>
      <td>LogsRetentionInDays</td>
      <td>Specifies the number of days you want to retain log events in the specified log group</td>
      <td>14</td>
      <td>no</td>
      <td>[1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653]</td>
    </tr>
  </tbody>
</table>

## Outputs

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Interface</th>
      <th>Description</th>
      <th>Exported?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ModuleId</td>
      <td>global</td>
      <td>Id of the module</td>
      <td>no</td>
    </tr>
    <tr>
      <td>ModuleVersion</td>
      <td>global</td>
      <td>Version of the module</td>
      <td>no</td>
    </tr>
    <tr>
      <td>StackName</td>
      <td>global</td>
      <td>Name of the stack (used to pass module references)</td>
      <td>no</td>
    </tr>
    <tr>
      <td>Arn</td>
      <td>ExposeArn</td>
      <td>Cluster ARN</td>
      <td>yes</td>
    </tr>
  </tbody>
</table>

## Limitations

* Scalable: Auto scaling is not provided by MSK service. You have to increase the number of broker nodes to scale manually.
* Secure: Does not backup/snapshot the data.
