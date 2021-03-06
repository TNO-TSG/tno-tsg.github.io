---
layout: doc
title: IDS Messages
toc: true
left_menu: true
slug: communication-ids-messages
---

The IDS Information Model provides an ontology for the description of metadata in the communication between IDS components. For the exchange of data between two connectors, the `ids:Message` concept is required to annotate the exchange with metedata about the exchange.

The IDS Information Model ontology can be found on [Github](https://github.com/International-Data-Spaces-Association/InformationModel).

The usage of these messages are described in [Message Flows]({{ '/docs/communication-message-flows/#policy-negotiation' | relative_url }}).

## Overview
The `ids:Message` class is divided into three groups of messages: Request Messages, Response Message, and Notification Messages. These groups all share a common set of properties that are present in all instantiations of the `ids:Message` class and provides generic meta information of the message.

In the table below these properties are listed. Summarizing, the `issuerConnector`, `recipientConnector`, `senderAgent`, and `recipientAgent` properties provide information of the intended recipient and sender of the message. The `securityToken`, `authorizationToken`, and `transferContract` properties are related to Identification, Authentication, and Authorization concepts. The `modelVersion`, `issued`, `contentVersion` properties provide generic information of the data exchange.

| Property | Cardinality | Description |
| --- | --- | --- |
| modelVersion | 1..1 | Information Model version, against which the Message should be interpreted |
| issued | 1..1 | Date of issuing the message |
| correlationMessage | ..1 | Correlated message. Usually needed, if a messages responds to a previous message. A Connector may, e.g., send a MessageProcessedNotification as a response to an incoming message and therefore needs this property to refer to the incoming message.	|
| issuerConnector | 1..1 | Origin Connector of the message |
| recipientConnector | 1..* | Target Connector |
| senderAgent | 1..1 | Agent, which initiated the message |
| recipientAgent | 1..* | Agent, for which the message is intended |
| securityToken | 1..1 | Token representing a claim, that the sender supports a certain security profile |
| authorizationToken | 1..1 | Authorization token |
| transferContract | ..1 | Contract which is (or will be) the legal basis of the data transfer |
| contentVersion | 0..* | Version of the content in the payload |

<center><strong>ids:Message property overview</strong></center>

For a complete overview of the different types of `ids:Message` classes and their accompanying properties, visit [Information Model Message Descriptions](http://htmlpreview.github.io/?https://github.com/International-Data-Spaces-Association/InformationModel/blob/feature/message_taxonomy_description/model/communication/Message_Description.htm). The remainder of this page is an representation of this overview, with additional information on different aspects of the classes and properties.

## Request Messages

The first sub-class of messages are the `ids:RequestMessage`s, that act as requests in traditional request-response communication patterns. An `ids:RequestMessage` can be instantiated directly or one of the message types displayed in the table below can be used.

| Message Class | Description | Additional Properties |
|---|---|---|
| RequestMessage | Client-generated message initiating a communication, motivated by a certain reason and with an answer expected. May be used for messages, which are not covered by the core IDS messages. | - |
| CommandMessage | Command messages are usually sent when a response is expected by the sender. Changes state on the recipient side. Therefore, commands are not 'safe' in the sense of REST. | - |
| InvokeOperationMessage | Message requesting the recipient to invoke a specific operation. | [`operationReference` (1..1)](# "References the operation to be invoked on the recipient's side by an URI") |
| ContractRequestMessage | Message containing a suggested content contract (as offered by the data consumer to the data provider) in the associated payload (which is an instance of ids:ContractRequest). | - |
| ArtifactRequestMessage | Message asking for retrieving the specified Artifact as the payload of an ArtifactResponse message. | [`requestedArtifact` (1..1)](# "References an ids:Artifact") |
| AccessTokenRequestMessage | Message requesting an access token. This is intended for point-to-point communication with, e.g., Brokers. | - |
| QueryMessage | Query message intended to be consumed by specific components. | [`queryLanguage` (..1)](# "the query language (see ids:QueryLanguage class") [`queryScope` (..1)](# "address all / active / inactive components (see ids:QueryScope class)") [`recipientScope` (..1)](# "specify target components (see ids:QueryTarget class)") |
| DescriptionRequestMessage | Message requesting metadata. If no URI is supplied via the ids:requestedElement field, this messages is treated like a self-description request and the recipient should return its self-description via an ids:DescriptionResponseMessage. However, if a URI is supplied, the Connector should either return metadata about the requested element via an ids:DescriptionResponseMessage, or send an ids:RejectionMessage, e.g. because the element was not found | [`requestedElement` (..1)](# "The element whose metadata is requested") |
| UploadMessage | Message used to upload a data to a recipient. Payload contains data | - |
| AppRegistrationRequestMessage | Message that asks for registration or update of a data app to the App Store. Payload contains app-related metadata (instance of class ids:AppResource). Message header may contain an app identifier parameter of a prior registered data app. If the app identifier is supplied, the message should be interpreted as a registration for an app update. Otherwise this message is used to register a new app. | [`affectedDataApp` (1..1)](# "IRI reference to the affected data app that is referenced in the App-related message") |
| AppUploadMessage | Message that usually follows a AppRegistrationResponseMessage and is used to upload a data app to the app store. Payload contains data app. Note that the message must refer to the prior sent, corresponding AppResource instance. The IRI of the ids:appArtifactReference must must match the IRI of the artifact which is the value for the ids:instance property. The ids:instance is specific for each representation. Therefore, if someone wants to upload multiple representations for an app, he has to state them using multiple ids:instance properties inside the AppRepresentation (and therefore inside the AppResource). Otherwise no mapping between payload and app metadata can be achieved. | [`appArtifactReference` (1..1)](# "IRI reference to the ids:Artifact, whose corresponding data is transfered as payload of the AppUploadMessage. The Artifact IRI reference must match the IRI of the instance IRI for the corresponding ids:AppRepresentation") |

<center><strong>ids:RequestMessage sub-classes</strong></center>

## Response Messages

The second sub-class of messages are the `ids:ResponseMessage`s, that act as responses to received requests. An `ids:ResponseMessage` must provide the `correlationMessage` property which references to the identifier of the received requests. An `ids:ResponseMessage` can be instantiated directly or one of the message types displayed in the table below can be used.

| Message Class | Description | Additional Properties |
|---|---|---|
| ResponseMessage | Response messages hold information about the reaction of a recipient to a formerly sent command or event. They must be correlated to this message. May be used for messages, which are not covered by the core IDS messages. | - |
| ArtifactResponseMessage | Message that follows up a ArtifactRequestMessage and contains the Artifact's data in the payload section. | - |
| AccessTokenResponseMessage | Response to an access token request, intended for point-to-point communication. | - |
| ContractAgreementMessage | Message containing a contract, as an instance of ids:ContractAgreement, with resource access modalities on which two parties have agreed in the payload. | - |
| ContractResponseMessage | Message containing a response to a contract request (of a data consumer) in form of a counter-proposal of a contract in the associated payload (which is an instance of ContractOffcer). | - |
| ResultMessage | Result messages are intended to annotate the results of a query command. | - |
| RejectionMessage | Rejection messages are specialized response messages that notify the sender of a message that processing of this message has failed. | [`rejectionReason` (..1)](# "Code describing the Rejection Reason, e.g., idsc:NOT_FOUND. (see ids:RejectionReason class)") |
| OperationResultMessage | Message indicating that the result of a former InvokeOperation message is available. May transfer the result data in its associated payload section | - |
| ParticipantResponseMessage | This class is deprecated. Use ids:DescriptionResponseMessage instead. ParticipantResponseMessage follows up a ParticipantRequestMessage and contains the Participant's information in the payload section. | - |
| ContractRejectionMessage | Message indicating rejection of a contract. | [`contractRejectionReason` (..1)](# "Human-readable text describing the reason for contract rejection") |
| DescriptionResponseMessage | Message containing the metadata, which a Connector previously requested via the ids:DescriptionRequestMessage, in its payload. | - |
| UploadResponseMessage | Message that follows up a UploadMessage and contains the upload confirmation. | - |
| AppUploadResponseMessage | Message that follows up an AppUploadMessage and contains the app upload confimation. | - |
| AppRegistrationResponseMessage | Message that follows up an AppRegistrationRequestMessage and contains the app registration confimation. | - |

<center><strong>ids:ResponseMessage sub-classes</strong></center>

> _Note: for all ids:ResponseMessage (sub)-classes the correlationMessage property, as described in ids:Message, is required._

## Notification Messages

The last sub-class of messages are the `ids:NotificationMessage`s, that act as notification without the requirement of receiving an response to the message. These messages are often used to indicate that a message is received properly or to send an update without explicit confirmations. An `ids:NotificationMessage` can be instantiated directly or one of the message types displayed in the table below can be used.

| Message Class | Description | Additional Properties |
|---|---|---|
| NotificationMessage | Notification messages are informative and no response is expected by the sender. May be used for scenarios, which are not covered by the core IDS messages. | - |
| LogMessage | Log Message which can be used to transfer logs e.g. to the clearing house. | - |
| ContractOfferMessage | Message containing a offered content contract (as offered by a data provider to the data consumer) in the associated payload (which is an instance of ContractOffer). In contrast to the ids:ContractResponseMessage, the ids:ContractOfferMessage is not related to a previous contract request. | - |
| ContractSupplementMessage | Message containing supplemental information to access resources of a contract. | [`agreedContract` (1..1)](# "Reference to a ids:ContractAgreement, which both parties agreed to") |
| MessageProcessedNotificationMessage | Notification that a message has been successfully processed (i.e., not ignored or rejected). | [`correlationMessage` (1..1)](# "ids:Message property that is required for this notification") |
| RequestInProcessMessage | Notification that a request has been accepted and is being processed. | [`correlationMessage` (1..1)](# "ids:Message property that is required for this notification") |
| **ConnectorNotificationMessage** |  |  |
| ConnectorInactiveMessage | Event notifying the recipient(s) that a connector will be unavailable. The same connector may be available again in the future | [`affectedConnector` (1..1)](# "References the affected Connector") |
| ConnectorUpdateMessage | Event notifying the recipient(s) about the availability and current configuration of a connector. The payload of the message must contain the updated connector's self-description | [`affectedConnector` (1..1)](# "References the affected Connector") |
| ConnectorCertificateGrantedMessage | Whenever a Connector has been successfully certified by the Certification Body, the Identity Provider can use this message to notify Infrastructure Components. | [`affectedConnector` (1..1)](# "References the affected Connector") |
| ConnectorCertificateRevokedMessage | Indicates that a (previously certified) Connector is no more certified. This could happen, for instance, if the Certification Body revokes a granted certificate or if the certificate has just expired. | [`affectedConnector` (1..1)](# "References the affected Connector") |
| **ResourceNotificationMessage** |  |  |
| ResourceUnavailableMessage | Message indicating that a specific resource is unavailable. The same resource may be available again in the future. | [`affectedResource` (1..1)](# "References the affected resource of the notification") |
| ResourceUpdateMessage | Message indicating the availability and current description of a specific resource. The resource must be present in the payload of this message. | [`affectedResource` (1..1)](# "References the affected resource of the notification") |
| **AppNotificationMessage** |  |  |
| AppAvailableMessage | Message indicating that a specific App should be available (again) in the AppStore. | [`affectedResource` (1..1)](# "References the affected resource of the notification") |
| AppUnavailableMessage | Message indicating that a specific App should be unavailable in the AppStore. | [`affectedResource` (1..1)](# "References the affected resource of the notification") |
| AppDeleteMessage | Message indicating that an App should be deleted from the AppStore. | [`affectedResource` (1..1)](# "References the affected resource of the notification") |
| **ParticipantNotificationMessage** |  |  |
| ParticipantUnavailableMessage | Event notifying the recipient(s) that a participant will be unavailable. The same participant may be available again in the future. | [`affectedParticipant` (1..1)](# "References the affected Participant of the notification") |
| ParticipantUpdateMessage | Event notifying the recipient(s) about the availability and current description of a participant. The payload of the message must contain the participant's self-description | [`affectedParticipant` (1..1)](# "References the affected Participant of the notification") |
| ParticipantCertificateGrantedMessage | Whenever a Participant has been successfully certified by the Certification Body, the Identity Provider can use this message to notify Infrastructure Components | [`affectedParticipant` (1..1)](# "References the affected Participant of the notification") |
| ParticipantCertificateUnavailableMessage | Indicates that a (previously certified) Participant is no more certified. This could happen, for instance, if the Certification Body revokes a granted certificate or if the certificate has just expired. | [`affectedParticipant` (1..1)](# "References the affected Participant of the notification") |

<center><strong>ids:NotificationMessage sub-classes</strong></center>
