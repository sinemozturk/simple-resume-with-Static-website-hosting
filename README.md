###  Register a domain with Route53

- Sign in to the AWS Management Console and open the Route 53 console at https://console.aws.amazon.com/route53/

- In the navigation pane, choose "Registered Domains"

- You can either create a new domain or transfer your existing domain (it can take little bit of time.)

- Enter the domain name that you want to register, and choose Check to find out whether the domain name is available.

- If the domain name that you want to register contains characters other than a-z, A-Z, 0-9, and - (hyphen), note the following:

    - You can enter the name using the applicable characters. You don't need to convert the name to Punycode.

    - A list of languages appears. Choose the language of the specified name. For example, if you enter příklad ("example" in Czech), choose Czech (CES) or Czech (CZE).

    - Note
    "For languages that have more than one code, you might need to try both of them. Even though CES and CZE are synonymous, some TLD registries support only one or the other."

- Choose Continue.

- On the Contact Details for Your n Domains page, enter contact information for the domain registrant, administrator, and technical contacts. The values that you enter here are applied to all of the domains that you're registering

- Choose Continue.

- Some TLDs only – If you specified an email address for the registrant contact that has never been used to register a domain with Route 53, some TLD registries require you to verify that the address is valid.

- If the registry requires verification and if it's possible to verify the address during domain registration, the console displays a Verify the Email Address for the Registrant Contact section:

    - If the section doesn't appear, skip to step 8.

    - If the section appears and the status is email-address is verified, skip to step 8.

    - If the section appears and the value is Registrant email not verified, continue with this step.

- Perform the following steps:

- Choose Send verification email. We send a verification email from one of the following email addresses:

    - noreply@registrar.amazon.com – for TLDs registered by Amazon Registrar.

    - noreply@domainnameverification.net – for TLDs registered by our registrar associate, Gandi. To determine who the registrar is for your TLD, see Domains that you can register with Amazon Route 53.
- When you receive the verification email, choose the link in the email that verifies that the email address is valid. If you don't receive the email immediately, check your junk email folder.

- Return to the Route 53 console. If the status doesn't automatically update to say email-address is verified, choose Refresh status.

- Choose whether you want us to automatically renew your domain registration before the expiration date.

- Review the information that you entered, read the terms of service, and select the check box to confirm that you've read the terms of service.

- Choose Complete Purchase. 

- When domain registration is complete, your next step depends on whether you want to use Route 53 or another DNS service as the DNS service for the domain:

    - Route 53 – In the hosted zone that Route 53 created when you registered the domain, create records to tell Route 53 how you want to route traffic for the domain and subdomains.

    - For example, when someone enters your domain name in a browser and that query is forwarded to Route 53, do you want Route 53 to respond to the query with the IP address of a web server in your data center or with the name of an ELB load balancer? 

- now we need records! 



###  Create a Bucket for Static WebSite Hosting 

- Go to S3 service from AWS Console

- Go to Buckets

- Create a bucket named with your domain name "www.yourdomainname.com"

- You can select the region, I used "us-east-1"

- Leave "object ownership" section as is

- Untick the "Block Public Access" setting for bucket

- It will give you warning but tick the "I acknowledge that the current settings might result in this bucket and the objects within becoming public."

- You can enabled the versioning "it is up to you" 

- Add tag if you want

- You can chose "default encryption" if you want

- Leave the advance setting as is. 

- Create the bucket


###  Create a Policy for Static WebSite Hosting 

- Go to your "www.yourdomainname.com" bucket 

- Upload Files named "index.html" , "error.html" and "your resume.pdf"  

- Click the "properties"

- Scroll down to " Static Website Hosting" and click edit

- Static Website hosting -> "Enable"

- Hosting type -> "Host a static website" 

- Indext document -> "index.html"

- Error document - optional -> "error.html"

- Redirection rules - optional leave it as is

- save it. 

- now you have a "bucket website endpoint" 

- Go to "permission" 

- Permissions>>> Bucket Policy >>> Paste bucket Policy

```bash
{
    "Version": "2012-10-17", 
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::don't forget to change me/*"
        }
    ]
}

```

- Now we have a static website it is open but endpoint is so long let's see how we will connect this one with our domain name 

###  DNS Record for Static WebSite Hosting

### Create A Record with "www" subdomain:

- Go to Route 53 service

- Click hosted zones on the left hand menu

- click your Domain name's public hosted zone "www.yourdomain.com"

- click "create record"

- select "simple routing" ---> Next

- click "Define simple record"

- Create A record with N. Virginia_

###  Alias for S3 bucket 

- click your Domain name's public hosted zone

- click "create record"

- select "simple routing" ---> Next

- click "Define simple record"

```bash
Record Name:"www"
Value/Route traffic to: 
    - Alias to *****S3 Website Endpoint*******
    - US East (N.Virginia) [us-east-1]
    - choose your S3 bucket named "www.[your DNS name].com"
Record Type : A
```
- hit the define simple record

- go to the target domain name "www.[your DNS name].net" on browser

- show the content of web page. It is the same as  S3 static web hosting page.


