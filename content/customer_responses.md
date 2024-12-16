# Technical support responses

*Where to view this portfolio piece: on this page.*

---

I currently work as a customer support technician at [Fastmail](https://www.fastmail.com/), an Australian email service provider. In this role, I'm responsible for guiding customers through technical configuration challenges each and every day.

Many of the challenges our customers face involve complex technical systems, including DNS and email authentication protocols.

See below for a couple of customer support requests I received recently, along with my responses. To protect customer privacy, I've redacted details and rewritten these requests, while ensuring the core of the question remains the same.

----

## Request 1: What's wrong with my DKIM setup?



> Hello Fastmail Support WIzards!
>
> I'm trying to set up another domain (example.com) in my Fastmail account, but I seem to be having issues getting the DKIM records to be accepted/marked as verified by Fastmail. I've set up the authoritative DNS (Cloudflare) as specified in your documentation, and it matches the configuration used by my other domains set up in Fastmail. Could you please have a look and let me know if I'm missing something? All other record types show as verified successfully.
>
> Configuration of the Cloudflare Zone is managed by Terraform.
>
> Please see the attached screenshots of my DNS record configuration in the Cloudflare control panel.
>
> Cheers,
>
> **$customer_name**

## Response 1: Disable CNAME flattening

>  Hello **$customer_name,**
>
>  Thank you for reaching out to Fastmail support! I'm sorry to hear that you're having issues with your DKIM records for `example.com`, but I'm happy to assist.
>
>  I've taken a look at your DNS records for `example.com`, and I can see that your three DKIM records appear to be set correctly. 
>
>  However, as you note, they don't seem to be resolving as we'd expect. We sometimes see domains hosted on Cloudflare flattening CNAME records, which can cause issues with our DKIM records. This is mentioned briefly in our Cloudflare help article: https://www.fastmail.help/hc/en-us/articles/1500000277962-Adding-MX-records-to-Cloudflare.
>
>  While I can see in the attached screenshot that you have CNAME flattening disabled for each of these DKIM records individually, some Cloudflare accounts have an additional option that enables flattening for all CNAME records added to a zone.
>
>  As a first troubleshooting step, may I ask that you log into your Cloudflare account to check if the **CNAME flattening for all CNAME records** option is turned on for the relevant zone? The steps required to do so are described in the following Cloudflare article: https://developers.cloudflare.com/dns/cname-flattening/set-up-cname-flattening/.
>
>  If this option is enabled, please disable it, and then check back in your Fastmail account to see if the DKIM records are resolving correctly. 
>
>  If it's not enabled, let me know, and I'll be happy to provide further troubleshooting steps.
>
>  Please let me know you go with this.
>
>
>  Cheers,
>  Jack



---

## Support request 2: Why is my SPF record failing?



> Hi,
>
> Can I clarify something with you? This is about SPF.
>
> I want to be able to send mail from other mail servers (Cloudflare and Google), in addition to Fastmail's. Where do I put those server details? Do I need to add them to my existing SPF record, or should I create a new one?
>
> I recently merged my existing records into one SPF record for example.com:
> ```
> v=spf1 include:_spf.mx.cloudflare.net include:spf.fastmail.com include:mxlogin.com include:_spf.google.com ?all
> ```
>
> However, Fastmail gives me an error for this record. Please advise.
>
> **$customer_name**

## Response 2: Not valid, adjust record

> Hello **$customer_name**,
>
> Thanks for getting back in touch with Fastmail support. I'm happy to clarify.
>
> Merging your separate Sender Policy Framework (SPF) records into a singular record was the right choice here, as an SPF records will fail to validate if more than one is added to a domain.
>
> The issue in this case is that the SPF specification requires the number of lookups for each record to be at most 10. This is specified in the official SPF specification, which you can view here: https://www.rfc-editor.org/rfc/rfc4408#section-10.1
>
> > SPF implementations MUST limit the number of mechanisms and modifiers that do DNS lookups to at most 10 per SPF check, including any lookups caused by the use of the "include" mechanism or the "redirect" modifier.
>
> While on first glance it may appear that your SPF record has less than 10 lookups (as your record only specifies four "include" mechanisms), there are actually 11 lookups total, as some of these "include" mechanisms contained nested "include" mechanisms when queried.
>
> I note that you want to be able to send via Fastmail, Google and Cloudflare. May I ask if  you also need to be able to send via mxlogin.com? 
>
> If not, I recommend you remove the `include:mxlogin.com` mechanism from your SPF record, as doing so will bring the number of lookups below 10,  and should resolve the error your seeing.
>
> Please let me know how you go with this.
>
> Cheers,
> Jack

---