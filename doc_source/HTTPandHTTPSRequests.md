# How CloudFront processes HTTP and HTTPS requests<a name="HTTPandHTTPSRequests"></a>

For Amazon S3 origins, CloudFront accepts requests in both HTTP and HTTPS protocols for objects in a CloudFront distribution by default\. CloudFront then forwards the requests to your Amazon S3 bucket using the same protocol in which the requests were made\. 

For custom origins, when you create your distribution, you can specify how CloudFront accesses your origin: HTTP only, or matching the protocol that is used by the viewer\. For more information about how CloudFront handles HTTP and HTTPS requests for custom origins, see [Protocols](RequestAndResponseBehaviorCustomOrigin.md#RequestCustomProtocols)\.

For information about how to restrict your distribution so that end users can only access objects using HTTPS, see [Using HTTPS with CloudFront](using-https.md)\.

**Note**  
The charge for HTTPS requests is higher than the charge for HTTP requests\. For more information about billing rates, go to the [CloudFront pricing plan](https://aws.amazon.com/cloudfront/#pricing)\.