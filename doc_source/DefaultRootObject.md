# Specifying a default root object<a name="DefaultRootObject"></a>

You can configure CloudFront to return a specific object \(the default root object\) when a user requests the root URL for your distribution instead of requesting an object in your distribution\. Specifying a default root object lets you avoid exposing the contents of your distribution\.

**Topics**
+ [How to specify a default root object](#DefaultRootObjectHowToDefine)
+ [How default root object works](#DefaultRootObjectHow)
+ [How CloudFront works if you don’t define a root object](#DefaultRootObjectNotSet)

## How to specify a default root object<a name="DefaultRootObjectHowToDefine"></a>

To avoid exposing the contents of your distribution or returning an error, specify a default root object for your distribution by completing the following steps\.<a name="DefaultRootObjectProcedure"></a>

**To specify a default root object for your distribution**

1. Upload the default root object to the origin that your distribution points to\.

   The file can be any type supported by CloudFront\. For a list of constraints on the file name, see the description of the `DefaultRootObject` element in [DistributionConfig](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_DistributionConfig.html)\.
**Note**  
If the file name of the default root object is too long or contains an invalid character, CloudFront returns the error `HTTP 400 Bad Request - InvalidDefaultRootObject`\. In addition, CloudFront caches the code for 10 seconds \(by default\) and writes the results to the access logs\.

1. Confirm that the permissions for the object grant CloudFront at least `read` access\.

   For more information about Amazon S3 permissions, see [Identity and access management in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-access-control.html) in the *Amazon Simple Storage Service User Guide*\.

1. Update your distribution to refer to the default root object using the CloudFront console or the CloudFront API\.

   To specify a default root object using the CloudFront console:

   1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

   1. In the list of distributions in the top pane, select the distribution to update\.

   1. In the **Settings** pane, on the **General** tab, choose **Edit**\.

   1. In the **Edit settings** dialog box, in the **Default root object** field, enter the file name of the default root object\.

      Enter only the object name, for example, `index.html`\. Do not add a `/` before the object name\.

   1. Choose **Save changes**\.

   To update your configuration using the CloudFront API, you specify a value for the `DefaultRootObject` element in your distribution\. For information about using the CloudFront API to specify a default root object, see [UpdateDistribution](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_UpdateDistribution.html) in the *Amazon CloudFront API Reference*\.

1. Confirm that you have enabled the default root object by requesting your root URL\. If your browser doesn't display the default root object, perform the following steps:

   1. Confirm that your distribution is fully deployed by viewing the status of your distribution in the CloudFront console\.

   1. Repeat steps 2 and 3 to verify that you granted the correct permissions and that you correctly updated the configuration of your distribution to specify the default root object\.

## How default root object works<a name="DefaultRootObjectHow"></a>

Suppose the following request points to the object `image.jpg`:

`https://d111111abcdef8.cloudfront.net/image.jpg`

In contrast, the following request points to the root URL of the same distribution instead of to a specific object, as in the first example:

`https://d111111abcdef8.cloudfront.net/`

When you define a default root object, an end\-user request that calls the root of your distribution returns the default root object\. For example, if you designate the file `index.html` as your default root object, a request for:

`https://d111111abcdef8.cloudfront.net/`

Returns:

`https://d111111abcdef8.cloudfront.net/index.html`

However, if you define a default root object, an end\-user request for a subdirectory of your distribution does not return the default root object\. For example, suppose `index.html` is your default root object and that CloudFront receives an end\-user request for the `install` directory under your CloudFront distribution:

`https://d111111abcdef8.cloudfront.net/install/`

CloudFront does not return the default root object even if a copy of `index.html` appears in the `install` directory\.

If you configure your distribution to allow all of the HTTP methods that CloudFront supports, the default root object applies to all methods\. For example, if your default root object is index\.php and you write your application to submit a `POST` request to the root of your domain \(https://example\.com\), CloudFront sends the request to https://example\.com/index\.php\.

The behavior of CloudFront default root objects is different from the behavior of Amazon S3 index documents\. When you configure an Amazon S3 bucket as a website and specify the index document, Amazon S3 returns the index document even if a user requests a subdirectory in the bucket\. \(A copy of the index document must appear in every subdirectory\.\) For more information about configuring Amazon S3 buckets as websites and about index documents, see the [Hosting Websites on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html) chapter in the *Amazon Simple Storage Service User Guide*\.

**Important**  
Remember that a default root object applies only to your CloudFront distribution\. You still need to manage security for your origin\. For example, if you are using an Amazon S3 origin, you still need to set your Amazon S3 bucket ACLs appropriately to ensure the level of access you want on your bucket\.

## How CloudFront works if you don’t define a root object<a name="DefaultRootObjectNotSet"></a>

If you don't define a default root object, requests for the root of your distribution pass to your origin server\. If you are using an Amazon S3 origin, any of the following might be returned:
+ **A list of the contents of your Amazon S3 bucket** – Under any of the following conditions, the contents of your origin are visible to anyone who uses CloudFront to access your distribution:
  + Your bucket is not properly configured\. 
  + The Amazon S3 permissions on the bucket associated with your distribution and on the objects in the bucket grant access to *everyone*\.
  + An end user accesses your origin using your origin root URL\. 
+ **A list of the private contents of your origin** – If you configure your origin as a private distribution \(only you and CloudFront have access\), the contents of the Amazon S3 bucket associated with your distribution are visible to anyone who has the credentials to access your distribution through CloudFront\. In this case, users are not able to access your content through your origin root URL\. For more information about distributing private content, see [Serving private content with signed URLs and signed cookies](PrivateContent.md)\.
+ `Error 403 Forbidden`—CloudFront returns this error if the permissions on the Amazon S3 bucket associated with your distribution or the permissions on the objects in that bucket deny access to CloudFront and to everyone\.