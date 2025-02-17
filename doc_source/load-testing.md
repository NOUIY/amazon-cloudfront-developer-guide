# Load testing CloudFront<a name="load-testing"></a>

Traditional load testing methods don't work well with CloudFront because CloudFront uses DNS to balance loads across geographically dispersed edge locations and within each edge location\. When a client requests content from CloudFront, the client receives a DNS response that includes a set of IP addresses\. If you test by sending requests to just one of the IP addresses that DNS returns, you're testing only a small subset of the resources in one CloudFront edge location, which doesn't accurately represent actual traffic patterns\. Depending on the volume of data requested, testing in this way may overload and degrade the performance of that small subset of CloudFront servers\.

CloudFront is designed to scale for viewers that have different client IP addresses and different DNS resolvers across multiple geographic regions\. To perform load testing that accurately assesses CloudFront performance, we recommend that you do all of the following:
+ Send client requests from multiple geographic regions\.
+ Configure your test so each client makes an independent DNS request; each client will then receive a different set of IP addresses from DNS\.
+ For each client that is making requests, spread your client requests across the set of IP addresses that are returned by DNS, which ensures that the load is distributed across multiple servers in a CloudFront edge location\.

Note the following restrictions for load testing CloudFront:
+ Load testing is not allowed on cache behaviors that have Lambda@Edge [viewer request or viewer response triggers](lambda-cloudfront-trigger-events.md)\.
+ Load testing is not allowed on origins that have [Origin Shield](origin-shield.md) enabled\.