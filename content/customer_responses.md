# Customer support responses

*Where to view this piece: on this page.*

---

I currently work as a customer support technician at [Fastmail](https://www.fastmail.com/), an Australian email service provider. 

Many of the issues our customers face involve complex technical systems, like the Domain Name System (DNS) and opaque email authentication measures. I'm responsible for guiding customers through the actions they need to take so that their accounts interface successfully with these systems.

Here are two of my recent responses to support requests[^1].

----

## Request 1

> Hello Fastmail Support WIzards/Witches!
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

## Response 1

>  Hello **$customer_name,**
>
>  Thank you for reaching out to Fastmail support! I'm sorry to hear that you're experiencing issues with your DKIM records for `example.com`. Please allow me to assist.
>
>  I've taken a look at your DNS records for `example.com`, and I can see that your three DKIM records appear to be set correctly. However, as you note, they don't seem to be resolving on our end. 
>
>  We sometimes see domains hosted on Cloudflare flattening CNAME records, which can cause issues with our DKIM records. This is mentioned briefly in our Cloudflare help article: https://www.fastmail.help/hc/en-us/articles/1500000277962-Adding-MX-records-to-Cloudflare.
>
>  While I can see in the attached screenshot that you have CNAME flattening disabled for each of these individual DKIM records, some Cloudflare accounts have an additional option that enables flattening for all CNAME records added to a zone.
>
>  So, as a first troubleshooting step, may I ask that you log into your Cloudflare account to check if the **CNAME flattening for all CNAME records** option is turned on for the relevant zone? The steps required check this are described under the **For all CNAME records** heading in the following Cloudflare article: https://developers.cloudflare.com/dns/cname-flattening/set-up-cname-flattening/#for-all-cname-records
>
>  If this option is:
>
>  - Enabled, please disable it, and then check back on your DKIM record status in your Fastmail account.
>  - Not enabled, let me know, and I'll be happy to provide further troubleshooting steps.
>
>  Please get back to me at your convenience.
>
>
>  Cheers,
>  Jack



---

## Request 2

> Hi,
>
> Can I clarify something with you? It's about SPF.
>
> I want to be able to send mail from other mail servers (Cloudflare and Google), in addition to Fastmail. Where do I put those server details? Do I need to add them to my existing SPF record, or should I create a new one?
>
> I recently merged my existing records into one SPF record for example.com:
> ```
> v=spf1 include:_spf.mx.cloudflare.net include:spf.fastmail.com include:mxexample.com include:_spf.google.com ?all
> ```
>
> However, Fastmail gives me an error for this record. Please advise.
>
> **$customer_name**

## Response 2

> Hello **$customer_name**,
>
> Thanks for getting in touch with Fastmail support. I'm happy to clarify.
>
> Merging your separate Sender Policy Framework (SPF) records into a singular record was the right choice here, as an SPF records won't work if more than one is added to the same domain.
>
> The issue here is that the SPF specification requires the number of DNS lookups for each record to be at most 10. This is specified in the official SPF specification, which you can view here: https://www.rfc-editor.org/rfc/rfc4408#section-10.1
>
> > SPF implementations MUST limit the number of mechanisms and modifiers that do DNS lookups to at most 10 per SPF check, including any lookups caused by the use of the "include" mechanism or the "redirect" modifier.
>
> While on first glance it may appear that your SPF record has less than 10 lookups (as your record only specifies four "include" mechanisms), there are actually 11 lookups total, as some of these "include" mechanisms contained nested "include" mechanisms that perform additional lookups when queried.
>
> I note that you want to be able to send via Fastmail, Google and Cloudflare. May I ask if you also need to be able to send via `mxexample.com`? 
>
> If not, I recommend you remove the `include:mxlogin.com` mechanism from your SPF record, as doing so will bring the number of lookups below 10,  and should thus resolve the error your seeing.
>
> Please let me know how you go with this.
>
> Cheers,
> Jack

[^1]: To protect customer privacy, I've rewritten the customer's initial request, and changed some details, The core content of the concern, and my responses, remain the same.