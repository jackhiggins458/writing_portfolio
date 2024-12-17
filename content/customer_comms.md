# DNS record change guide

*Where to view this portfolio piece: on this page.*

---

As mentioned in [Technical support responses]( {{< relref "customer_responses.md" >}} ), I currently work at [Fastmail](https://www.fastmail.com/). 

Thousands of customers use Fastmail to host one or more simple static websites. Due to upcoming changes to Fastmail's internal networks, the IP addresses that customers used to host their websites on Fastmail were changing. We could make these changes automatically for customers hosting their DNS records at Fastmail, but we needed all other customers to manually update their DNS records at their host.

I wrote a set of guides (emailed to over a thousand affected customers) to explain the situation, and to outline the steps customers would need to take to ensure their websites remained online.

Here's a (redacted) version of the guide I wrote that was sent to a customer. This particular message was sent to a customer with:

- Only one website hosted at Fastmail.
-  Their DNS hosted by Cloudflare[^1].

---

> Dear **$customer_name**,
>
> Our systems show that you are currently hosting a website on your Fastmail account. They also show that the DNS for this website is hosted at an external DNS provider (Cloudflare). As a result of some upcoming technical changes on our end, we’ll need you to update the DNS records for this domain at Cloudflare if you wish to continue hosting these websites at Fastmail. 
>
> When you host a website at the root (or apex) of your domain, you have to enter the IP addresses of the web server hosting your website. Because you are using an external DNS provider for your domain, you entered the IP addresses of the Fastmail web server on the external DNS provider's (Cloudflare's) control panel. These IP addresses are changing, and you will need to update them at Cloudflare by **$current_date + 2 months**. 
>
> We have identified the following domains that need updating:
>
> - **$example.tld**
>
> As mentioned above, the DNS for this domain appears to be hosted by Cloudflare. Cloudflare supports a feature called "CNAME flattening", which we recommend using instead of specific IP addresses.
>
> To set this up, please follow these steps:
>
> 1. Log in to your [Cloudflare dashboard](https://dash.cloudflare.com/login) and select your account.
> 2. Find your **$example.tld** domain in the domain list and click on it.
> 3. Go to **DNS > DNS Records** at the top right of the screen.
> 4. For each **$example.tld** record, you should see one or more A type records with content of the form **$current_ip** .
> 5. If there is more than one A type record for **$example.tld** , click **Edit** and then **Delete** on each of them until there is only one A record left.
> 6. Click **Edit** on the final A record for **$example.tld**.
> 7. Change the **Type** to **CNAME** .
> 8. Change the **Target** to **web.fastmail.com**.
> 9. Ensure **Proxy status** is **DNS only**.
> 10. Click **Save**.
>
> The DNS for your domain will now point to our new IP addresses. These changes should not cause any interruption to the access of your website.
>
> Please note that if the described changes are not made by **$current_date + 2 months**, your domain will no longer point to your website content, and the website will no longer be accessible via the domain. This means that anyone who entered **$example.tld** online would not be able to view your website.
>
> If you have any questions or concerns about this change, or any of the steps you need to take, please reply to this message. Our Support team will be happy to assist.

[^1]: Fastmail customers had domains hosted at many different hosts. Each host requires a slightly different set of steps to update DNS records. We identified that the majority of Fastmail customers used one of six hosts. I wrote a specific guide for each of these hosts, We sent each customer the guide matching their DNS host, allowing the majority of customers to receive exact, step by step instructions. Customers not hosting their domain on one of these six hosts received a generic set of instructions instead.
