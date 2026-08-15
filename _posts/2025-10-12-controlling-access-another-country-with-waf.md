---
title: Controling Access Another Country With WAF
date: 2025-10-12 9:25:30 +0700
categories: [aws, waf, security, portolio]
tags: [cloud, infrastructure, portofolio]     # TAG names should always be lowercase
---

![TEXT](/assets/img/post/wht_theck.png)

What is default access for all Country & Region??

**AWS WAF determines the country and region of a request based on its IP address. By default, AWS WAF uses the IP address of the web request's origin.**

I wanna trust for client for SG & ID, its possible? I think, yes its possible.

You can use the geo match statement for country or region matching, as follows:

1. Country — You can use a geo match rule by itself to manage requests based solely on their country of origin. The rule statement matches against country codes. You can also follow a geo match rule with a label match rule that matches on the country of origin label.
2. Region — Use a geo match rule followed by a label match rule to manage requests based on their region of origin. You can't use a geo match rule alone to match against region codes.

Note
To filter traffic from Hong Kong, use the ISO 3166-1 alpha-2 country code HK in your geo match statement.

How can i block another country but only can access SG & ID?
1. You can create waf custom rules example: AllowSingaporeIndonesia
2. Or can you import this json for simplefy access
```bash
{
  "Name": "AllowSingaporeIndonesia",
  "Priority": 0,
  "Statement": {
    "GeoMatchStatement": {
      "CountryCodes": [
        "SG",
        "ID"
      ]
    }
  },
  "Action": {
    "Allow": {}
  },
  "VisibilityConfig": {
    "SampledRequestsEnabled": true,
    "CloudWatchMetricsEnabled": true,
    "MetricName": "AllowSingaporeIndonesia"
  }
}
```
3. Validate this json for rules.
4. Save this rules and you can top priority this rule for **priority 0**
5. After that, you can see traffic request with label

![TEXT](/assets/img/post/waf_based_country.png)

Referensi:
1. https://docs.aws.amazon.com/waf/latest/developerguide/waf-rule-statement-type-geo-match.html
2. https://repost.aws/knowledge-center/waf-allow-block-country-geolocation