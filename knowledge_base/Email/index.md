---
title: Email
draft: false
tags:
  - Email
aliases: 
description: How does email work?
permalink: 
enableToc: true
cssclasses: 
created: 2025-04-13  15:01
modified: 2025-04-13  16:20
published: 
comments: true
---

This is a primer on the core components of email - with a handy sequence diagram to walk you through the parts. 

# Email Authentication

Email Authentication is the methods in which we ensure that the email being sent/received is "authentic".

> [!example]- Want to spoof an email yourself? (Please don't...)
> It can really help illustrate why all of these things are important by demonstrating how easy it is to spoof an email.
> 
> If you go into your mail client of choice, you will note there is a "from" field (It is hidden by default in Outlook) - Add in a new/custom address
> 
> Congratulations! You are now spoofing an email address. 
> 
> *While it would actually require a step or two more to actually work - in concept this is all that is required.* 
 
> [!hint]+ Email Authentication Best Practices
> The "Messaging, Malware and Mobile Anti-Abuse Working Group" (M3AAWG)  have a very comprehensive guide on best practices for email authentication. Take a read!
>[ M3AAWG Email Authentication Recommended Best Practices](https://www.m3aawg.org/sites/default/files/m3aawg-email-authentication-recommended-best-practices-09-2020.pdf)

There are a 4 key components of modern email authentication - and all of them should be contained in a domains public DNS records. 

| Acronym | Formal Name                                                 | Standard                                               | Purpose                                                                                                                                                                                                                    |     |
| ------- | ----------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| MX      | Mail Exchange                                               | [RFC974](https://www.rfc-editor.org/rfc/rfc974.html)   | When a mail server is sending mail (typical mail sent via Simple Mail Transfer Protocol - SMTP) it will read this record to know who to send the mail to                                                                   |     |
| SPF     | Sender Policy Framework                                     | [RFC7208](https://www.rfc-editor.org/rfc/rfc7208.html) | A list of who is an authorized sender for a domain                                                                                                                                                                         |     |
| DKIM    | DomainKeys Identified Mail                                  | [RFC6376](https://www.rfc-editor.org/rfc/rfc6376.html) | Using public-key cryptography, a sending mail server will sign the header of an email. The public DKIM record is then used by the receiver to check the signature & ensure the mail is legitimate/the untouched in transit |     |
| DMARC   | Domain-based Message Authentication, Reporting, Conformance | [RFC7489](https://www.rfc-editor.org/rfc/rfc7489.html) | This instructs mail receivers on what to do with mail that does not pass SPF/DKIM                                                                                                                                          |     |

Sequence workflow to help illustrate what happens when:
```mermaid
sequenceDiagram
  autonumber
  Box Sender
  actor User as Sending User
  participant SMS as Sending Mail Server
  end
  participant DNS as DNS Records
  box Receiver
  participant RMS as Receiving Mail Server
  actor RUser as Receiving User
  end

  User ->> SMS: Mail send request
  Note left of SMS: Is this user allowed to send via this mail server?
  SMS ->> SMS: Authentication challenge
  break Authentication Failed
    SMS ->> User: Send request rejects
  end
  autonumber 3
  SMS ->> SMS: Authentication pass
  SMS ->> DNS: Get MX record for recipient
  Note over SMS: Who should Receive this mail?
  DNS ->> SMS: Return MX record
  SMS ->> RMS: Send mail to recipient mail servers
  Note over SMS: Sign mail header with DKIM private key
  RMS ->> DNS: Get SPF, DKIM, DMARC
  Note over RMS: What are the records for this sender?
  DNS ->> RMS: Return SPF, DKIM, DMARC
  RMS ->> RMS: Evaulate SPF
  Note over RMS: Is this Sender in the SPF record?
  break SPF Fail
    RMS ->> RMS: Evaulate DMARC
    note right of RMS: Send reports to RUA/RUF<br>value listed in DMARC record
  end
  autonumber 10
  RMS ->> RMS: SPF Pass
  RMS ->> RMS: Evaluate DKIM
  Note over RMS: Does this Senders mail header<br>signature match the public key?
  break DKIM Fail
    RMS ->> RMS: Evaluate DMARC
    note right of RMS: Send reports to RUA/RUF<br>value listed in DMARC record
  end
  autonumber 12
  RMS ->> RMS: DKIM Pass
  RMS ->> RMS: Custom Evaluation
  Note over RMS: Spam/malware/phishing checks /<br> Mailflow rules / etc
  RUser ->> RMS: Authenticate to server
  break Authentication Fail
    RMS ->> RUser: Authentication fail
  end
  autonumber 15
  RMS ->> RMS: Authentication pass
  RMS ->> RUser: Receive the Senders mail
```

