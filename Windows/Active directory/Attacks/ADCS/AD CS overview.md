To obtain a certificate from AD CS, clients go through a process called enrollment. The client generate a public-private key pair and place the public key in a certificate signing request (CSR) message along with other details such as the the subject of the certificate and the certificate template name. ***Clients then sign the CSR with their private key and send the CSR to CA server. 

The CA server checks if the client can request certificates. If so, it will issue a certificate by looking up the certificate template that was requested in the CSR. if the template is permissions allow authenticating this user, the CA generates certificate using the *blueprint* of the certificate template (e.g, EKUs, issuance requirements, .., ..) and using the other info that was supplied in the CSR. If everything checks out the CA will then sign the certificate using the CA's private key then returns it to the client.

![[Pasted image 20250721061318.png]]

some important EKUs with their OU:
![[Pasted image 20250721061858.png]]
these when present in the certificate, permit authentication to AD.

***
How it can be abused??


```mermaid |
  sequenceDiagram
    participant Attacker as Attacker (Certipy)
    participant KDC as Domain Controller (KDC)

    Note over Attacker: Load administrator.pfx<br/>(contains cert + private key)

    Attacker->>Attacker: Step 1: Sign AuthPack with private key
    Attacker->>KDC: Step 2: AS-REQ (PKINIT)<br/>includes certificate + signed AuthPack

    KDC->>KDC: Step 3: Extract public key from certificate
    KDC->>KDC: Step 4: Verify signature on AuthPack

    alt Signature valid
        KDC->>Attacker: Step 5: AS-REP with TGT
        Attacker->>KDC: Step 6: TGS-REQ (Request service ticket)
        KDC->>Attacker: Step 7: TGS-REP (Returns service ticket)
    else Invalid signature
        KDC->>Attacker: Step 5: KRB-ERROR (Pre-auth failed)
    end

    Note over Attacker: Step 8: Now holds TGT + service ticket<br/>Full impersonation of Administrator

```


<pre><code>```mermaid flowchart TD A[User (Low-priv)] -->|1. Discover vulnerable template| B[Cert Template: ClientAuth + ENROLLEE_SUPPLIES_SUBJECT] B -->|2. Request cert with DA UPN| C[Cert Issued] C -->|3. Export .pfx cert+key| D[PFX File] D -->|4. Use cert to get TGT via PKINIT| E[TGT for Domain Admin] ```</code></pre>
