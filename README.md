# Get Started with Retarus Cloud Fax Integration for SAP BTP

## Introduction

The aim of this Integration package is to give SAP BTP developers an out-of-the-box tool to get connected with *Retarus Cloud Fax REST API* with no need to develop the required code by themselves.

## Business Case

The pre-configured connector can be seamlessly integrated into business flows via SAP BTP and SAP Integration Suite and addresses organizations who want to modernize and automate their faxing while moving to SAP S/4HANA Cloud.

The Integration flows supplied here can be a part of wider integration scenarios or can be directly called by a web application with a basic capability of sending files over https by using a simple form-data.

## Prerequisites

1. Integration Suite enabled for your Subaccount in SAP BTP
2. Having a deal with Retarus about the Cloud Fax Service
    >if you still don't have it, you can contact Retarus here: [https://www.retarus.com/contact/](https://www.retarus.com/contact/)
3. Having received by Retarus the credentials to access the service

## Package Documentation & Donwload Files

To setup and use the Retarus Cloud Fax Integration, please refer to the relevant detailed documentation and download files:

- Integration Package: [docs](./docs/integration-flows/README.md) - [files](.\src\integration-flows\Retarus_Global_CloudFaxIntegration_S4H-RetCloudFax_V1.2.zip)

## Changelog

- v2.0.1:
  - removed the use of Open Connectors
  - added global variables for headers configuration
  - added value mapping for global value configuration
  - SendFax flows now can store the JobId returning from Retarus to be used later to poll the job status
  - GetStatusReport can be used in multiple ways: with last returned job id, with a specific job id or with no job id at all to get the status of all job ids
- v1.2:
  - updated naming convention
  - added capabilty of selecting the coverpage template from within the job

## Old versions - deprecated

If you are still running an old deprecated version of this package and still need a reference that you cannot find in the current documentation, here you can find the [removed documentation and resources](./docs/old-deprecated/README.md)
