This project is about creating a couple of static sites in S3, one for the apex, and the other
for www.

We want to host a JSON config file on the apex. Then redirect everything else to www.

* Files
  * The scripts start `u-01`, `u-02`, etc., bring up the static sites
  * `assets/` holds a file that is served from the apex; the script that sets up the apex bucket
    also sets the conten-type
  * The `templates/*.json` files are templates for configurations
  * `helpers/cloudfront-create` and `helpers/route53-create` are used by the other scripts

Using the scripts:

* Bringing it up
  * run the scripts u-01..., u-02..., etc.
* Bring it down
  * You will have to do that manually but something like the order of the d-01... scripts

To set everything up:

* Manual steps
  * In Route53, [create a public hosted zone for the domain at Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#). 
  * Change the DNS delegation to the addresses given for the NS record Route53 sets up for you 
    in the hosted zone. (In DNSimple, which I use as a registrar, the delegation can be changed under Registration.)
* `u-01-create-www-bucket`: sets up the target web site.
* `u-02-create-apex-bucket`: redirects to the target. The apex script has tests that verify the redirect: Therefore, the www site
needs to be in place first.
* `u-03-create-certificate`: Create the certificate that will be used by cloudfront
* `u-04-create-cloudfront-distribution`: Create the cloudfront distributions
* `u-05-redirect-apex-to-cloudfront`: Switch the redrect from the s3 www website to the cloudfront hostname
* `u-06-make-buckets-non-public`: 

Notes

1. You can also do the redirect with a LB. See https://aws.amazon.com/about-aws/whats-new/2018/07/elastic-load-balancing-announces-support-for-redirects-and-fixed-responses-for-application-load-balancer/
2. Here the buckets are public but they apparently don't need to be. See https://levelup.gitconnected.com/production-level-static-sites-in-aws-39573c9bf4d5
3. When you use the stack (https://github.com/aws-samples/amazon-cloudfront-secure-static-site/tree/master), it creates two different certs, one for www, the other for apex; see also https://github.com/aws-cloudformation/aws-cloudformation-templates/blob/main/S3/compliant-static-website.json

Useful sources

* https://levelup.gitconnected.com/production-level-static-sites-in-aws-39573c9bf4d5
* https://aws.amazon.com/blogs/networking-and-content-delivery/amazon-cloudfront-introduces-origin-access-control-oac/
* https://medium.com/@deshanranasinghe/expose-aws-s3-private-bucket-objects-via-cloudfront-signed-urls-fab678283622
* https://tutorialsdojo.com/using-origin-access-control-to-secure-s3-object-access-to-cloudfront/
* S3
  * https://spacelift.io/blog/aws-s3-cp
  * https://loige.co/aws-command-line-s3-content-from-stdin-or-to-stdout/
