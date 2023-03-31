# Controlling origin requests<a name="controlling-origin-requests"></a>

When a viewer request to CloudFront results in a *cache miss* \(the requested object is not cached at the edge location\), CloudFront sends a request to the origin to retrieve the object\. This is called an *origin request*\. The origin request always includes the following information from the viewer request:
+ The URL path \(the path only, without URL query strings or the domain name\)
+ The request body \(if there is one\)
+ The HTTP headers that CloudFront automatically includes in every origin request, including `Host`, `User-Agent`, and `X-Amz-Cf-Id`

Other information from the viewer request, such as URL query strings, HTTP headers, and cookies, is not included in the origin request by default\. But you might want to receive some of this other information at the origin, for example to collect data for analytics or telemetry\. You can use an *origin request policy* to control the information that's included in an origin request\. 

Origin request policies are separate from [cache policies](controlling-the-cache-key.md), which control the cache key\. This separation enables you to receive additional information at the origin and also maintain a good *cache hit ratio* \(the proportion of viewer requests that result in a cache hit\)\. You do this by separately controlling which information is included in origin requests \(using the origin request policy\) and which is included in the cache key \(using the cache policy\)\.

Although the two kinds of policies are separate, they are related\. All URL query strings, HTTP headers, and cookies that you include in the cache key \(using a cache policy\) are automatically included in origin requests\. Use the origin request policy to specify the information that you want to include in origin requests, but *not* include in the cache key\. Just like a cache policy, you attach an origin request policy to one or more cache behaviors in a CloudFront distribution\.

You can also use an origin request policy to add additional HTTP headers to an origin request that were not included in the viewer request\. These additional headers are added by CloudFront before sending the origin request, with header values that are determined automatically based on the viewer request\. For more information, see [Adding CloudFront request headers](adding-cloudfront-headers.md)\.

**Topics**
+ [Creating origin request policies](#origin-request-create-origin-request-policy)
+ [Understanding origin request policies](#origin-request-understand-origin-request-policy)
+ [Using the managed origin request policies](using-managed-origin-request-policies.md)
+ [Adding CloudFront request headers](adding-cloudfront-headers.md)
+ [Understanding how origin request policies and cache policies work together](understanding-how-origin-request-policies-and-cache-policies-work-together.md)

## Creating origin request policies<a name="origin-request-create-origin-request-policy"></a>

You can use an origin request policy to control the values \(URL query strings, HTTP headers, and cookies\) that are included in requests that CloudFront sends to your origin\. You can create an origin request policy in the CloudFront console, with the AWS Command Line Interface \(AWS CLI\), or with the CloudFront API\.

After you create an origin request policy, you attach it to one or more cache behaviors in a CloudFront distribution\.

Origin request policies are not required\. When a cache behavior does not have an origin request policy attached, the origin request includes all the values that are specified in the [cache policy](controlling-the-cache-key.md#cache-key-understand-cache-policy), but nothing more\.

**Note**  
To use an origin request policy, the cache behavior must also use a [cache policy](controlling-the-cache-key.md)\. You cannot use an origin request policy in a cache behavior without a cache policy\.

------
#### [ Console ]

**To create an origin request policy \(console\)**

1. Sign in to the AWS Management Console and open the **Policies** page in the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home?#/policies](https://console.aws.amazon.com/cloudfront/v3/home?#/policies)\.

1. Choose **Origin request**, then choose **Create origin request policy**\.

1. Choose the desired setting for this origin request policy\. For more information, see [Understanding origin request policies](#origin-request-understand-origin-request-policy)\.

1. When finished, choose **Create**\.

After you create an origin request policy, you can attach it to a cache behavior\.

**To attach an origin request policy to an existing distribution \(console\)**

1. Open the **Distributions** page in the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home#/distributions](https://console.aws.amazon.com/cloudfront/v3/home#/distributions)\.

1. Choose the distribution to update, then choose the **Behaviors** tab\.

1. Choose the cache behavior to update, then choose **Edit**\.

   Or, to create a new cache behavior, choose **Create behavior**\.

1. In the **Cache key and origin requests** section, make sure that **Cache policy and origin request policy** is chosen\.

1. For **Origin request policy**, choose the origin request policy to attach to this cache behavior\.

1. At the bottom of the page, choose **Save changes**\.

**To attach an origin request policy to a new distribution \(console\)**

1. Open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. Choose **Create distribution**\.

1. In the **Cache key and origin requests** section, make sure that **Cache policy and origin request policy** is chosen\.

1. For **Origin request policy**, choose the origin request policy to attach to this distribution's default cache behavior\.

1. Choose the desired settings for the origin, default cache behavior, and other distribution settings\. For more information, see [Values that you specify when you create or update a distribution](distribution-web-values-specify.md)\.

1. When finished, choose **Create distribution**\.

------
#### [ CLI ]

To create an origin request policy with the AWS Command Line Interface \(AWS CLI\), use the aws cloudfront create\-origin\-request\-policy command\. You can use an input file to provide the command's input parameters, rather than specifying each individual parameter as command line input\.

**To create an origin request policy \(CLI with input file\)**

1. Use the following command to create a file named `origin-request-policy.yaml` that contains all of the input parameters for the create\-origin\-request\-policy command\.

   ```
   aws cloudfront create-origin-request-policy --generate-cli-skeleton yaml-input > origin-request-policy.yaml
   ```

1. Open the file named `origin-request-policy.yaml` that you just created\. Edit the file to specify the origin request policy settings that you want, then save the file\. You can remove optional fields from the file, but don't remove the required fields\.

   For more information about the origin request policy settings, see [Understanding origin request policies](#origin-request-understand-origin-request-policy)\.

1. Use the following command to create the origin request policy using input parameters from the `origin-request-policy.yaml` file\.

   ```
   aws cloudfront create-origin-request-policy --cli-input-yaml file://origin-request-policy.yaml
   ```

   Make note of the `Id` value in the command's output\. This is the origin request policy ID, and you need it to attach the origin request policy to a CloudFront distribution's cache behavior\.

**To attach an origin request policy to an existing distribution \(CLI with input file\)**

1. Use the following command to save the distribution configuration for the CloudFront distribution that you want to update\. Replace *distribution\_ID* with the distribution's ID\.

   ```
   aws cloudfront get-distribution-config --id distribution_ID --output yaml > dist-config.yaml
   ```

1. Open the file named `dist-config.yaml` that you just created\. Edit the file, making the following changes to each cache behavior that you are updating to use an origin request policy\.
   + In the cache behavior, add a field named `OriginRequestPolicyId`\. For the field's value, use the origin request policy ID that you noted after creating the policy\.
   + Rename the `ETag` field to `IfMatch`, but don't change the field's value\.

   Save the file when finished\.

1. Use the following command to update the distribution to use the origin request policy\. Replace *distribution\_ID* with the distribution's ID\.

   ```
   aws cloudfront update-distribution --id distribution_ID --cli-input-yaml file://dist-config.yaml
   ```

**To attach an origin request policy to a new distribution \(CLI with input file\)**

1. Use the following command to create a file named `distribution.yaml` that contains all of the input parameters for the create\-distribution command\.

   ```
   aws cloudfront create-distribution --generate-cli-skeleton yaml-input > distribution.yaml
   ```

1. Open the file named `distribution.yaml` that you just created\. In the default cache behavior, in the `OriginRequestPolicyId` field, enter the origin request policy ID that you noted after creating the policy\. Continue editing the file to specify the distribution settings that you want, then save the file when finished\.

   For more information about the distribution settings, see [Values that you specify when you create or update a distribution](distribution-web-values-specify.md)\.

1. Use the following command to create the distribution using input parameters from the `distribution.yaml` file\.

   ```
   aws cloudfront create-distribution --cli-input-yaml file://distribution.yaml
   ```

------
#### [ API ]

To create an origin request policy with the CloudFront API, use [CreateOriginRequestPolicy](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_CreateOriginRequestPolicy.html)\. For more information about the fields that you specify in this API call, see [Understanding origin request policies](#origin-request-understand-origin-request-policy) and the API reference documentation for your AWS SDK or other API client\.

After you create an origin request policy, you can attach it to a cache behavior, using one of the following API calls:
+ To attach it to a cache behavior in an existing distribution, use [UpdateDistribution](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_UpdateDistribution.html)\.
+ To attach it to a cache behavior in a new distribution, use [CreateDistribution](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_CreateDistribution.html)\.

For both of these API calls, provide the origin request policy's ID in the `OriginRequestPolicyId` field, inside a cache behavior\. For more information about the other fields that you specify in these API calls, see [Values that you specify when you create or update a distribution](distribution-web-values-specify.md) and the API reference documentation for your AWS SDK or other API client\.

------

## Understanding origin request policies<a name="origin-request-understand-origin-request-policy"></a>

CloudFront provides some predefined origin request policies, known as *managed policies*, for common use cases\. You can use these managed policies, or you can create your own origin request policy that's specific to your needs\. For more information about the managed policies, see [Using the managed origin request policies](using-managed-origin-request-policies.md)\.

An origin request policy contains the following settings, which are categorized into *policy information* and *origin request settings*\.

### Policy information<a name="origin-request-understand-origin-request-policy-info"></a>

**Name**  
A name to identify the origin request policy\. In the console, you use the name to attach the origin request policy to a cache behavior\.

**Description**  
A comment to describe the origin request policy\. This is optional\.

### Origin request settings<a name="origin-request-understand-origin-request-policy-settings"></a>

Origin request settings specify the values in viewer requests that are included in requests that CloudFront sends to the origin \(known as origin requests\)\. The values can include URL query strings, HTTP headers, and cookies\. The values that you specify are included in origin requests, but are not included in the cache key\. For information about controlling the cache key, see [Controlling the cache key](controlling-the-cache-key.md)\.

**Headers**  
The HTTP headers in viewer requests that CloudFront includes in origin requests\. For headers, you can choose one of the following settings:  
+ **None** – The HTTP headers in viewer requests are *not* included in origin requests\.
+ **All viewer headers** – All HTTP headers in viewer requests are included in origin requests\.
+ **All viewer headers and the following CloudFront headers** – All HTTP headers in viewer requests are included in origin requests\. Additionally, you specify which of the CloudFront headers you want to add to origin requests\. For more information about the CloudFront headers, see [Adding CloudFront request headers](adding-cloudfront-headers.md)\.
+ **Include the following headers** – You specify which HTTP headers are included in origin requests\.
**Note**  
Do not specify a header that is already included in your **Origin Custom Headers** settings\. For more information, see [Configuring CloudFront to add custom headers to origin requests](add-origin-custom-headers.md#add-origin-custom-headers-configure)\.
+ **All viewer headers except** – You specify which HTTP headers are ***not*** included in origin requests\. All other HTTP headers in viewer requests, except for the ones specified, are included\.
When you use the **All viewer headers and the following CloudFront headers**, **Include the following headers**, or **All viewer headers except** setting, you specify HTTP headers by the header name only\. CloudFront includes the full header, including its value, in origin requests\.  
When you use the **All viewer headers except** setting to remove the viewer's `Host` header, CloudFront adds a new `Host` header with the origin's domain name to the origin request\.

**Cookies**  
The cookies in viewer requests that CloudFront includes in origin requests\. For cookies, you can choose one of the following settings:  
+ **None** – The cookies in viewer requests are *not* included in origin requests\.
+ **All** – All cookies in viewer requests are included in origin requests\.
+ **Include the following cookies** – You specify which cookies in viewer requests are included in origin requests\.
+ **All cookies except** – You specify which cookies in viewer requests are ***not*** included in origin requests\. All other cookies in viewer requests are included\.
When you use the **Include the following cookies** or **All cookies except** setting, you specify cookies by their name only\. CloudFront includes the full cookie, including its value, in origin requests\.

**Query strings**  
The URL query strings in viewer requests that CloudFront includes in origin requests\. For query strings, you can choose one of the following settings:  
+ **None** – The query strings in viewer requests are *not* included in origin requests\.
+ **All** – All query strings in viewer requests are included in origin requests\.
+ **Include the following query strings** – You specify which query strings in viewer requests are included in origin requests\.
+ **All query strings except** – You specify which query strings in viewer requests are ***not*** included in origin requests\. All other query strings are included\.
When you use the **Include the following query strings** or **All query strings except** setting, you specify query strings by their name only\. CloudFront includes the full query string, including its value, in origin requests\.