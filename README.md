# Azure CDN Guidance

Azure has several ways to host your Web Applications and Content.  The [Azure Content Delivery Network (CDN) service](https://azure.microsoft.com/en-us/services/cdn/) can assist the mass scale or global reach of your Web Applications and Content.  
If you are new to CDN's in general, you can start with a [Primer](https://github.com/andywahr/azure-cdn-guidance/wiki/CDN-Primer).

The Azure CDN service make it very easy add CDN capability to:

## Static Website and Assets
* [Static Websites hosted in Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/static-website-content-delivery-network)
* [Static Web Applications hosted in Azure App Services](https://docs.microsoft.com/en-us/azure/cdn/cdn-add-to-web-app)
* [Use an Azure Storage Account to host Static Assets (JS, CSS, Images, etc..)](https://docs.microsoft.com/en-us/azure/cdn/cdn-storage-custom-domain-https)
* [In front of anything hosting HTTP Traffic with Static Assets (even if it's not in Azure!)](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain)

## Dynamic Websites
* [Front Door for a Dynamic Web Site](https://docs.microsoft.com/en-us/azure/frontdoor/quickstart-create-front-door)
* Azure CDN with Verizon or Akamai Providers, optimized for [Dynamic Site Acceleration](https://docs.microsoft.com/en-us/azure/cdn/cdn-dynamic-site-acceleration)
  * [For App Service](https://docs.microsoft.com/en-us/azure/cdn/cdn-add-to-web-app)
  * [For Any Domain](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain)

# Caching
## What to Cache
The key to effectively using a CDN is to pick the appropriate content to Cache.  The [CDN Primer](https://github.com/andywahr/azure-cdn-guidance/wiki/CDN-Primer) talks about the kinds of content.  But normally, sites are a rich mixture of Static and Dynamic Content.  This is where you can use different DNS Domains to segregate kinds of content.  Here are some ideas:

### Dynamic website all on one domain
Let's take a site, called www.X.com, and it has Dynamic content generated by hosted backend cluster of webservers.  The Web Pages refer to Images, JS, and CSS all hosted in the application.  That means all of the resources are accessed via www.X.com.  The site's popularity is growing in a large geographic area and you need to improve load times.  There are 2 approaches:
1. Put Dynamic Site Acceleration on your entire site and add Cache rules for the paths for your static assets
   * You would use Azure CDN with Akamai or Verizon Providers, and configure the rules (discussed below)
   * Need to be careful that Cache Rules *ONLY* apply to static.
1. Create a new DNS Domain for Static Assets (cdn.X.com)
   * Dynamic Site Acceleration is still a recommended idea for www.X.com
   * Configure a General Web Delivery CDN Endpoint to point where www.X.com is hosted but configured for cdn.X.com.
     * Web Pages and Content would need to switch all references to www.X.com for the static assets to cdn.X.com.

### Limited time event causes significant (10X or more) increase in traffic
There are certain annual events (Black Friday, Boxing Day, Singles Day, etc..) or other limited time situations (COVID-19) than causes a dramatic increase in traffic.  Your site is starting to have operational or cost issues trying to keep up with the demand.  There are a few strategies to leverage CDN in a way to absorb that extra traffic:
* Alternative DNS Domain with Static Site (or full [Single Page Application](https://en.wikipedia.org/wiki/Single-page_application)) that is fully cacheable.
   * Create a deals.X.com or covid.X.com, and publish a Static site
   * Put a prominent link on your home page (www.X.com), directing users there
   * Setup Dynamic Site Acceleration in front with Caching on the Home Page, either:
     * Remove all Dynamic Content generated server side from your Home Page
     * Make a new fully Cacheable Home page with a link to your original Home Page
   * Temporarily remove any Dynamic content generated server side from your Home Page, and setup Dynamic Site Acceleration in front with Caching on the Home Page.
  
## How to Cache
For your assets to be cached, they must respond with the appropriate [Cache Headers](https://github.com/andywahr/azure-cdn-guidance/wiki/Cache-Headers).  Either your Origin has to respond with these Headers or you need to configure the Azure CDN Endpoint to augment or override them:
* [Microsoft, Standard Verizon, or Standard Akamai Provider](https://docs.microsoft.com/en-us/azure/cdn/cdn-caching-rules-tutorial)
* [Premium Verizon](https://docs.microsoft.com/en-us/azure/cdn/cdn-verizon-premium-rules-engine)

For troubleshooting, please refer to the [Troubleshooting Guide](https://github.com/andywahr/azure-cdn-guidance/wiki/CDN-Cache-Troubleshooting-Guide)
