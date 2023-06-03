# ResourceID

Proposal to add a new field, `resourceID` to PerformanceResourceTiming. This  [DOMString](https://webidl.spec.whatwg.org/#idl-DOMString) type attribute will add the capability to PerformanceResourceTiming to uniquely track different resource fetches

## Use cases

When collecting Real User Monitoring (RUM) data about resources, understanding the relationships between resources and identifying their initiator resources that trigger fetches is crucial. This knowledge enables RUM providers to analyze and optimize resource loading, such as identifying critical resources for the Largest Contentful Paint (LCP) element or identifying 3rd-party scripts that load additional scripts.

The FetchID attribute serves as a unique identifier associated with each resource fetch. By introducing the FetchID attribute, a solid foundation is established for the initiator attribute, enabling clear connections between resources and their initiators. This allows for tracing the hierarchy of fetches and determining parent-child relationships among resources, enabling the construction of a comprehensive dependency tree. 

Am accurate dependency tree can open a lot of interesting possiblities on optimization front which have been discussed in depth in the issue referenced below.

(Reference : [Resource initiator information to enable creation of dependency trees · Issue #263 · w3c/resource-timing](https://github.com/w3c/resource-timing/issues/263))

## API Changes and Example Code

The PerformanceResourceTiming Interface in <a  href="https://w3c.github.io/resource-timing/#sec-performanceresourcetiming">resource-timing</a> would be updated to

```bash
[Exposed=(Window,Worker)]

interface  PerformanceResourceTiming  :  PerformanceEntry  {
...
...
readonly attribute DOMString resourceID;
...
...
[Default] object  toJSON();
};
```

Sample usage:
```javascript
const  entry_list = performance.getEntriesByType("resource");
console.log(entry_list[0].resourceID);
```

## Desired Characteristics

The ResourceIDs should possess the following characteristics to effectively serve their purpose within the Resource Timing API:
1.  Uniqueness: Each ResourceID should be unique, allowing for differentiation between different fetch request for different resources and their corresponding Resource Timing Entries. 
2.  Randomness: It is desirable for ResourceIDs to exhibit a certain level of randomness to help prevent any patterns or correlations that could potentially compromise the integrity. 

## Implementation Details

  To meet the desired ResourceID characteristics, the already specified [randomUUID](https://w3c.github.io/webcrypto/#dfn-Crypto-method-randomUUID) method specified in the [Web Cryptography API ](https://w3c.github.io/webcrypto/) can be utilized. This method generates a Version 4 UUID (Universally Unique Identifier) that can serve as ResourceID attribute. 

## Potential Spec Changes

Fetch 
- New DOMString field ResourceID added to [fetch timing info](https://fetch.spec.whatwg.org/#fetch-timing-info)

- [Fetching](https://fetch.spec.whatwg.org/#fetching) is modified to set [fetch timing info](https://fetch.spec.whatwg.org/#fetch-timing-info)'s ResourceID field.

Resource Timing Level 2 

- [4.3](https://w3c.github.io/resource-timing/#sec-performanceresourcetiming) : Adding new field to interface : resourceID

- A [PerformanceResourceTiming](https://w3c.github.io/resource-timing/#dom-performanceresourcetiming) has an associated DOMString `resourceID`.

- Getter steps for `resourceID` returns `resourceID` from  `fetch-Timing-Info` Struct. 

## Security/Privacy Considerations

- The proposed field does not reveal any new cross-origin information.

### [Self-Review Questionnaire: Security and Privacy](https://w3ctag.github.io/security-questionnaire/)

> 01.  What information does this feature expose,
>      and for what purposes?

The resourceID feature exposes a unique identifier associated with each resource fetch. It is used to track different resource fetches and lay solid foundation for initiator attribute that can establish relationships between resources and their initiators for the purpose of analyzing and optimizing resource loading in Real User Monitoring (RUM) scenarios.


> 02.  Do features in your specification expose the minimum amount of information
>      necessary to implement the intended functionality?

Yes.

> 03.  Do the features in your specification expose personal information,
>      personally-identifiable information (PII), or information derived from
>      either?

No.

> 04.  How do the features in your specification deal with sensitive information?

The proposed feature does not deal with sensitive information. It is a randomly generated identifier that does not reveal any sensitive details.

> 05.  Do the features in your specification introduce state
>      that persists across browsing sessions?

No, the resourceID feature does not introduce state that persists across browsing sessions. It is specific to the current resource fetch and does not retain any information between sessions.

> 06.  Do the features in your specification expose information about the
>      underlying platform to origins?

No.

> 07.  Does this specification allow an origin to send data to the underlying
>      platform?

No, It does not allow an origin to send data to the underlying platform as it is a read-only attribute.

> 08.  Do features in this specification enable access to device sensors?

No.

> 09.  Do features in this specification enable new script execution/loading
>      mechanisms?

No.

> 10.  Do features in this specification allow an origin to access other devices?

No.

> 11.  Do features in this specification allow an origin some measure of control over
>      a user agent's native UI?

No.

> 12.  What temporary identifiers do the features in this specification create or
>      expose to the web?

The resourceID feature creates a temporary (randomly generated) identifier specific to each resource fetch.It can be accessed via Resource Timing API.

> 13.  How does this specification distinguish between behavior in first-party and
>      third-party contexts?

No Distinction.

> 14.  How do the features in this specification work in the context of a browser’s
>      Private Browsing or Incognito mode?

Works same in both the scenarios.

> 15.  Does this specification have both "Security Considerations" and "Privacy
>      Considerations" sections?

No.

> 16.  Do features in your specification enable origins to downgrade default
>      security protections?

No.

> 17.  What happens when a document that uses your feature is kept alive in BFCache
>      (instead of getting destroyed) after navigation, and potentially gets reused
>      on future navigations back to the document?



> 18.  What happens when a document that uses your feature gets disconnected?



> 19.  What should this questionnaire have asked?

None.
