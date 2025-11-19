# Setting up a test case

## Summary

- [Setting up a test case](#setting-up-a-test-case)
  - [Summary](#summary)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
    - [Retarus](#retarus)
    - [SAP BTP](#sap-btp)
  - [Test Setup](#test-setup)
    - [1. Setting up Retarus part](#1-setting-up-retarus-part)
    - [2. SAP Part: getting the Endpoint URL](#2-sap-part-getting-the-endpoint-url)
    - [3. Preparing the payload](#3-preparing-the-payload)
    - [4. Examples](#4-examples)
      - [4.1. Basic use case - no coverpage](#41-basic-use-case---no-coverpage)
      - [4.2. Full use case - all coverpage variables setup](#42-full-use-case---all-coverpage-variables-setup)

## Introduction

This document will guide you through the process of setting up a test case of the Cloud Fax Connector for SAP Solutions.

To test the Cloud Fax Connector, we will use the iFlow called [sendFaxExternalInterface](../../docs/integration-flows/README.md#3-sendfaxexternalinterface), supplied with this package, to provide a public endpoint URL that will give access to the iFlow [cloudfaxflowpost](../../docs/integration-flows/README.md#1-cloudfaxflowpost) which, in turn, will send a payload to Retarus Cloud Fax Service.

The payload will be a sample *.pdf* file sent via a *form-data* type message.

Beside the sample *.pdf* file, this package include a *.txt* file feature the *cURL command* to send the *form-data* and a *.html* file to be used as coverletter for the fax.

We will see these files and how to use them in details in the [Test Setup](#test-setup) section.

## Prerequisites

### Retarus

- *Retarus Cloud Fax Service* is fully setup and ready to be used.
  >*Note: in case you don't know or you're not sure, you can check either by logging in to the EAS portal, or by contacting the Retarus Implementation Engineer that implemented the service for you, or by inquiring [support@retarus.com](mailto:support@retarus.com)*
- You can access the *Retarus Enterprise Administration Service (EAS) portal*.
- You have the *credentials* to authenticate to the *Cloud Fax Service*.
- You know your *Customer Number*.

### SAP BTP

- You have the *credentials* to authenticate to the deployed iFlows.
- You have already *imported and deployed* the *Integration flows* supplied with this package (if not, please follow these [instructions](../../docs/integration-flows/README.md)).
- You have already setup the required *Open Connector* (if not, please follow these [instructions](../../docs/api-integration/README.md)) and the relevant credentials to use in the iFlows.
- You have downloaded the files in the sample folder:
  - [coverpage-default.ftl.html](coverpage-default.ftl.html)
  - [curlPostFax.txt](curlPostFax.txt)
  - [faxSample.pdf](faxSample.pdf)

## Test Setup

### 1. Setting up Retarus part

Retarus allows to store a *coverpage template* in your Cloud Fax settings in EAS portal.

This coverpage is automatically added to all faxes that are being sent out through your Cloud Fax account, it is a *.html* file and it contains placeholder variables that can be replaced by the values you specify in your payload headers (see the [payload details](../../docs/integration-flows/README.md#13-input-payload)).

The file supplied here, i.e. [coverpage-default.ftl.html](coverpage-default.ftl.html), is the default template that Retarus supplies to all customers. It cannot be changed or customized by yourself but you can check its structure to see how the placeholders are called, so that you can setup your payload properly.
>*Note: to change or customize your coverpage template, please refer to your Retarus representative*

This is the *HTML* code of the coverpage file:

```html
<#escape x as x?html>
<#setting time_zone="Europe/Berlin"/>
<html>
  <head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <title>Retarus Faxolution for Applications Default Cover Page</title>
    <style type="text/css">
      body {font-family:Verdana, Arial;}
      hr {color:black; background-color:black; height:2px; text-align:center; border:0px;}
    </style>
  </head>
  <body>
    <div style="padding-top:50px">&nbsp;</div>
    <div style="padding-left:70px"><h1>Telefax</h1></div>
    <div style="padding-top:50px">&nbsp;</div>
    
    <table width="95%" align="center">
      <colgroup width="150"></colgroup>
      <tr><td colspan="2"><hr></td></tr>
      <tr><td><strong>From:</strong></td><td>${FromName!}</td></tr>
      <tr><td><strong>Company:</strong></td><td>${FromCompanyName!}</td></tr>
      <tr><td><strong>Telephone:</strong></td><td>${FromTelNum!}</td></tr>
      
      <tr><td colspan="2"><hr></td></tr>
      <tr><td><strong>To:</strong></td><td>${ToName!}</td></tr>
      <tr><td><strong>Company:</strong></td><td>${ToCompanyName!}</td></tr>
      <tr><td><strong>Fax Number:</strong></td><td>${ToFaxNum!}</td></tr>
      
      <tr><td colspan="2"><hr></td></tr>
      
      <tr><td><strong>Subject:</strong></td><td>${SubjectTitle!}</td></tr>
      <tr><td colspan="2">${SubjectText!}</td></tr>
    </table>
  </body>
</html>
</#escape>
```

The variables you see enclosed by brackets are the aforementioned placeholders, for instance *\$\{FromName\}* or *\$\{ToFaxNum\}*.

For all the other settings you may need to do in your Retaus Cloud Fax account, please refer to the official [Retarus documentation](https://docs.retarus.com/fax/configuration-in-the-eas-portal-vs-via-a-job).

### 2. SAP Part: getting the Endpoint URL

If the prerequisites have been respected, you should have already deployed all the iFlows in the package, including the one called [sendFaxExternalInterface](../../docs/integration-flows/README.md#3-sendfaxexternalinterface).

This iFlow is meant to provide a public interface for the one in charge of building up the REST job and forward it to Retarus and can be also used for running tests.

Open your Integration Suite instance, go to *Monitor => Integrations and APIs* then select the tile ***All*** in the *Manage Integration Content* section.

Now click on *sendFaxEtenernalInterface* in the list of the deployed iFlows and copy the endpoint URL you will find there.

Leave out the */http/sendFaxFacade* part, you should get something like:

[https://retarus-integrations-eu.it-cpiNNN-rt.cfapps.euXX-YYY.hana.ondemand.com](https://retarus-integrations-eu.it-cpiNNN-rt.cfapps.euXX-YYY.hana.ondemand.com)

Keep this URL for the next step.

### 3. Preparing the payload

The file [curlPostFax.txt](./samples/curlPostFax.txt) contains a cURL command which you can use to send the supplied sample file [faxSample.pdf](./samples/faxSample.pdf) as fax.

If you rename that file into a *.bat* file, it will be possible to execute it direcly, after you have adjusted the missing parts.
>*Note: also be sure to have both the .pdf and the .bat file in the same folder*

This is the file content:

```js
curl --location '{yourIntegrationSuiteAddress}/http/sendFaxFacade' \
--header 'destFaxNumber: {yourFaxNumber}' \
--header 'csid: {yourCSID}' \
--header 'key01: FromName' \
--header 'value01: {yourFromName} ' \
--header 'key02: ToName' \
--header 'value02: {yourToName}' \
--header 'key03: SubjectTitle' \
--header 'value03: {tourSubjecttitle}' \
--header 'cuno: {yourCustomerNumber}' \
--header 'Authorization: Basic {yourCredentialsB64}' \
--form 'file=@"faxSample.pdf"'
```

You need to replace the bracketed variables with the relevant values. These are the mandatory fields:

- *{yourIntegrationSuiteAddress}*: this is the basic address of your deployed iFlow, you retrieved this in the [previous step](#2-sap-part-getting-the-endpoint-url).
- *{yourFaxNumber}*: this is the recipient's fax number. e.g. +49123456789.
- *{yourCSID}*: this is the fax Called Subscriber Identification.
- *{yourCustomerNumber}*: this is the Customer Number assigned by Retarus.
- *{yourCredentialsB64}*: this is the Base64 encoding of the credentials you need to authenticate to SAP. They must be in the format *username:password* and then encoded.

The other header values are used to fill out the coverpage placeholder variables.
The *keyNN* headers address the variable in the coverpage template (*FromName*, for example) while the *valueNN* headers provide the values those variables must be replaced with.

If you don't want to use a coverpage or you don't want to set values for the coverpage variables, you can remove the relevant headers.
If you need, instead, to have more variables addressed, then add the header you need with the same syntax.

### 4. Examples

Here are some examples of cURL commands you can use depending on your need. You can modify the [curlPostFax.txt](./samples/curlPostFax.txt) file based on these examples and on what you would like to test:

#### 4.1. Basic use case - no coverpage

```js
curl --location 'https://retarus-integrations-eu.it-cpiNNN-rt.cfapps.euXX-YYY.hana.ondemand.com/http/sendFaxFacade' \
--header 'destFaxNumber: +49123456789' \
--header 'csid: RetarusCloudFax' \
--header 'cuno: 12345' \
--header 'Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=' \
--form 'file=@"faxSample.pdf"'
```

>*Note: here no header about the coverpage variables has been considered*

#### 4.2. Full use case - all coverpage variables setup

```js
curl --location 'https://retarus-integrations-eu.it-cpiNNN-rt.cfapps.euXX-YYY.hana.ondemand.com/http/sendFaxFacade' \
--header 'destFaxNumber: +49123456789' \
--header 'csid: RetarusCloudFax' \
--header 'key01: FromName' \
--header 'value01: Cloud Fax Sender ' \
--header 'key02: ToName' \
--header 'value02: John Doe' \
--header 'key03: SubjectTitle' \
--header 'value03: Retarus Cloud Fax Conncetor for SAP solution testing' \
--header 'key04: FromCompanyName' \
--header 'value04: SAP Integration Suite' \
--header 'key05: FromTelNum' \
--header 'value05: +4998765431' \
--header 'key06: ToCompanyName' \
--header 'value06: Retarus' \
--header 'key07: ToFaxNum' \
--header 'value07: +49123456789' \
--header 'key07: SubjectText' \
--header 'value07: This is a test Fax sent from SAP Integration Suite via Retarus Cloud Fax service' \
--header 'cuno: 12345' \
--header 'Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=' \
--form 'file=@"faxSample.pdf"'
```

>*Note: in this example all variables will be replaced by a value and, as you can see, they don't need to be recalled in the same order in which they appear in the coverletter*
