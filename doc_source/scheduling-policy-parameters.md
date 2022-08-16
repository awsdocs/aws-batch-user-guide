# Scheduling policy parameters<a name="scheduling-policy-parameters"></a>

Scheduling policies are split into three basic components: the name, fair share policy, and tags of the scheduling policy\.

**Topics**
+ [Scheduling policy name](#scheduling-policy-name)
+ [Fair share policy](#scheduling-policy-fair_share_policy)
+ [Tags](#scheduling-policy-tags)

## Scheduling policy name<a name="scheduling-policy-name"></a>

`name`  
The name for your scheduling policy\. Up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\.  
Type: String  
Required: Yes

## Fair share policy<a name="scheduling-policy-fair_share_policy"></a>

`fairsharePolicy`  
The fair share policy of the scheduling policy\.  

```
"fairsharePolicy": {
   "computeReservation": number,
   "shareDecaySeconds": number,
   "shareDistribution": [
      {
         "shareIdentifier": "string",
         "weightFactor": number
      }
   ]
}
```
Type: Object  
Required: No    
`computeReservation`  
A value used to reserve some of the available maximum VCPU for fair share identifiers that have not yet been used\.  
The reserved ratio is `(computeReservation/100)^ActiveFairShares` where *ActiveFairShares* is the number of active fair share identifiers\.  
For example, a `computeReservation` value of 50 indicates that AWS Batch should reserve 50% of the maximum available VCPU if there is only one active fair share identifier, 25% if there are two active fair share identifiers, and 12\.5% if there are three active fair share identifiers\. A `computeReservation` value of 25 indicates that AWS Batch should reserve 25% of the maximum available VCPU if there is only one active fair share identifier, 6\.25% if there are two active fair share identifiers, and 1\.56% if there are three active fair share identifiers\.  
Type: Integer  
Valid range: Minimum value of 0\. Maximum value of 99\.  
Required: No  
`shareDecaySeconds`  
The time period to use to calculate a fair share percentage for each fair share identifier in use\. A value of zero \(0\) indicates that only current usage should be measured\. The decay allows for more recently run jobs to have more weight than jobs that ran earlier\.  
Type: Integer  
Valid range: Minimum value of 0\. Maximum value of 604800 \(1 week\)\.  
Required: No  
`shareDistribution`  
Array of objects that contain the weights for the fair share identifiers for the fair share policy\. Fair share identifiers that are not included have a default weight of `1.0`\.  

```
"shareDistribution": [
   {
      "shareIdentifier": "string",
      "weightFactor": number
   }
]
```
Type: Array  
Required: No    
`shareIdentifier`  
A fair share identifier or fair share identifier prefix\. If the string ends with '\*' then this string specifies a fair share identifier prefix for fair share identifiers that begin with that prefix\. For example if the value is `UserA*` and the `weightFactor` is 1 and there are two fair share identifiers that begin with `UserA`, then each of those fair share identifiers will have a weight of 2; if there are five such fair share identifiers, then each would have a weight of 5\.  
The list of fair share identifiers and fair share identifier prefixes in a fair share policy cannot overlap\. For example you cannot have a fair share identifier prefix of `UserA*` and a fair share identifier of `UserA-1` in the same fair share policy\.  
Type: String  
Required: Yes  
`weightFactor`  
The weight factor for the fair share identifier\. The default value is 1\.0\. A lower value has a higher priority for compute resources\. For example, jobs that use a share identifier with a weight factor of 0\.125 \(1/8\) get 8 times the compute resources of jobs that use a share identifier with a weight factor of 1\.  
The smallest supported value is 0\.0001 and the largest supported value is 999\.9999\.  
Type: Float  
Required: No

## Tags<a name="scheduling-policy-tags"></a>

`tags`  
Key\-value pair tags to associate with the scheduling policy\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.  
Type: String to string map  
Required: No