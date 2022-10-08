# How to Host a Static Website with Google Cloud Storage and Cloudflare

This article will explain how to host a website with a custom domain on Google Cloud Storage. Cloudflare will be used to get the site working with https, though you could alternatively use a load balancer like Google Cloud Load Balancer.

1. Verify your domain name in [Google Search Console](https://search.google.com/search-console). Instructions for that are [here](https://cloud.google.com/storage/docs/domain-name-verification), and will involve adding a TXT record to your domain DNS (note: do NOT add any Google-related CNAME records yet, this caused verification to fail for me)
2. Create Google Cloud Storage bucket with same name as your domain name.
3. In your Google Cloud Storage bucket Permissions, click “Grant Access”, in the “New principles” box type “allUsers” and select the role Cloud Storage → Storage Object Viewer. Adding this role will open your bucket up to the public.
4. Go back to your [list of buckets](https://console.cloud.google.com/storage/browser). Next to your bucket, click on the three-dot menu on the rightmost column, and click “Edit website configuration”. In the “Index (main) page suffix” box type “index.html”, and in the 404 box type 404.html.
5. Upload your website to the bucket. At a minimum you will need a index.html file and a CNAME file containing your domain name (eg. jbernier.com). You can upload to your bucket with one of the following commands:

Sync local files to bucket:

```bash
gsutil rsync -r out gs://jbernier.com
```

Copy local files to bucket:

```bash
gsutil cp -r out gs://jbernier.app
```

Ok now your site will work with your custom domain over http, but not https. Google Cloud Storage does not support https out of the box, so we will need to handle that via a load balancer or a service like Cloudflare. Cloudflare has a free plan with [generous limits](https://developers.cloudflare.com/workers/platform/limits/#:~:text=Cloudflare%20does%20not%20enforce%20response,5%20GB%20for%20Enterprise%20customers.) of 100,000 reads per day, so in this tutorial we will go with Cloudflare.

**Setting up Cloudflare**

1. Sign up for the [Cloudflare free plan](https://www.cloudflare.com/plans/free/)
2. Update your DNS nameservers in your domain hosting settings as instructed
3. In Cloudflare DNS settings, add a CNAME record pointing to “[c.storage.googleapis.com](http://c.storage.googleapis.com/)”

That’s it! If certain files aren’t loading (eg. images), you can purge the cache in Cloudflare’s dashboard.

In another article I’ll discuss how to set up Google Cloud Run with a custom domain name.

Alternative articles:

- [geekflare - How to Host Static Website on Google Cloud Storage?](https://geekflare.com/cloud-storage-static-website/)
- [geekflare - How to Setup a SSL for Google Cloud Storage hosted Site?](https://geekflare.com/gcs-site-over-https/)
