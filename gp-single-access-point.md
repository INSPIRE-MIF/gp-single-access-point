Head Office of Geodesy and Cartography

# **Guidance for the integration of dispersed WMS sources**

Version 1.0.20

21st 1st December 2020

**Warsaw; 2020**

# Table of contents

**[Normative reference 3](#_Toc59466269)**

**[Terms 4](#_Toc59466270)**

**[Introduction 5](#_Toc59466271)**

**[Standards 7](#_Toc59466272)**

[Supported operations 7](#_Toc59466273)

_[GetCapabilities 7](#_Toc59466274)_

_[GetMap 7](#_Toc59466275)_

_[GetFeatueInfo 9](#_Toc59466276)_

**[Standardisation of local services 10](#_Toc59466277)**

_[CRSs 10](#_Toc59466278)_

_[Names of layer 10](#_Toc59466279)_

_[Portrayal of layers 10](#_Toc59466280)_

_[Structure of GetFeatureInfo responses 10](#_Toc59466281)_

**[Examples of implementations 11](#_Toc59466282)**

[Technical solution 11](#_Toc59466283)

**[Final words 12](#_Toc59466284)**

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Normative reference 3**](#_Toc57650729)

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Terms 4**](#_Toc57650730)

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Introduction 5**](#_Toc57650731)

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Standards 7**](#_Toc57650732)

[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** Supported operations 7](#_Toc57650733)

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** GetCapabilities 7](#_Toc57650734)_

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** GetMap 7](#_Toc57650735)_

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** GetFeatueInfo 9](#_Toc57650736)_

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Standardisation of local services 10**](#_Toc57650737)

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** CRSs 10](#_Toc57650738)_

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** Names of layer 10](#_Toc57650739)_

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** Portrayal of layers 10](#_Toc57650740)_

_[**Błąd! Nieprawidłowy odsyłacz typu hiperłącze.** Structure of GetFeatureInfo responses 10](#_Toc57650741)_

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Examples of implementations 11**](#_Toc57650742)

[Błąd! Nieprawidłowy odsyłacz typu hiperłącze. **Final words 12**](#_Toc57650743)

# Normative reference

This guidance contains references to or derives concepts froorm the following documents:

INSPIRE, Implementing Directive 2007/2/EC of the European Parliament and of the Council as

regards interoperability of spatial data sets and service

INSPIRE, INS NS, Commission Regulation (EC) No 976/2009 of 19 October 2009 implementing

Directive 2007/2/EC of the European Parliament and of the Council as regards the Network Services

INSPIRE, INS DS, Commission Regulation (EU) No 1089/2010 of 23 November 2010 implementing

Directive 2007/2/EC of the European Parliament and of the Council as regards interoperability of

spatial data sets and services

INSPIRE, INS DSTG, Technical Guidance for the implementation of INSPIRE Discovery Services

OGC 06-042 OpenGIS Web Map Service (WMS) Implementation Specification

OGC 09-025r2 Web Feature Service 2.0 Interface Standard – With Corrigendum

OGC 05-078r4, OGC SLD, OGC™ Styled Layer Descriptor profile of the Web Map Service

Implementation Specification, version 1.1.0 (Release 4) and its corrigendum1 for OGC Implementation

Specification SLD 1.1.0 (07-123r1)

OGC 06-121r3 – OGC Web Services Common Specification (OWS) 1.1.0

# Terms

**collective service**

proxy network service working on top and integrating many network services providing the same or similar spatial data sets in terms of data model but with the different spatial extent

**discovery services**

making it possible to search for spatial data sets and services on the basis of the content of the corresponding metadata and to display the content of the metadata [INSPIRE Directive]

**download services**

enabling copies of spatial data sets, or parts of such sets, to be downloaded and, where practicable, accessed directly [INSPIRE Directive]

**integrating, proxy service**

intermediary network service working on top and integrating many network services providing the same or similar spatial data set but with the different spatial extent

**metadata**

information describing spatial data sets and spatial

**network services**

network services should make it possible to discover, transform, view and download spatial data and to invoke spatial data and e-commerce services [INSPIRE Directive]

**spatial data**

data with a direct or indirect reference to a specific location or geographic area [INSPIRE Directive]

**spatial data set**

identifiable collection of spatial data [INSPIRE Directive]

**view service**

making it possible, as a minimum, to display, navigate, zoom in/out, pan, or overlay viewable spatial data sets and to display legend information and any relevant content of metadata [INSPIRE Directive]

# Introduction

The responsibility for maintenance of spatial data sets differs across European Union (EU) countries. The data set belonging to the same specific INSPIRE thematic category depending on the country can be maintained on the national level, regional and local level. For example, cadastral data sets in countries like Spain or Czechia and maintained on the national level whereas in Poland the data is maintained locally.

Maintaining similar spatial data sets on a regional or local level leads to a dispersion of data sets and hinders users&#39; access to the spatial data. A user who wants to access and use the data published by many local or regional organisations has to put some additional effort to first find and later integrate data from many data sources. And if there are hundreds of such data sources it may require many hours of additional work or investing extra resources on integration services provided by specialised third parties.

To avoid that a proxy service can be created, integrating collective services maintained on local, regional or even national level. Such proxy service form user perspective offers one seamless data set for the bigger area. Figure 1Figure 1 shows the concept of integrating collective service.

![](RackMultipart20210209-4-rdgy8z_html_c4d87ac73b46d36d.gif)

_Figure 1 Concept of the integrating collective service_

It is important to mention that while proxy service gives users access to data covering a bigger area, a user can still connect directly to a local service.

INSPIRE discovery services natively utilize the concept of service integration. Catalogue Service for Web (CS-W) standard supports Harvest operation that as its name suggest can harvest metadata records from local catalogue servers to a parent catalogue. Therefore, a user of parent (e.g. national) metadata catalogue has access to the metadata provided by child catalogues (e.g. regional or local). Similarly, users via EU INSPIRE metadata catalogue have access to the content of national catalogues.

However, such an approach is not natively supported in case of INSPIRE view and download services. Therefore, some additional technical solutions have to be implemented to facilitate access to local data.

This document provides guidelines on developing such proxy collective service for view services utilising Web Map Service (WMS) standard.

# Standards

The integrating, proxycollective service should support WMS standards in at least two last and most popular versions 1.1.1 and 1.3.0. On the time of the creation of this document, it seems that the proposed solution can also be applied for new OGC API – Maps standard that is still under development.

## Supported operations

The proxy service should support at least GetCapabilities and GetMap operations. However, it is recommended that GetFeatureInfo operation is also supported.

Proxy service from a service&#39;s user perspective works like any other WMS service. However, its backend differs from the backend of typical WMS service. The similarities and differences are explained in the following chapters:

- GetCapabilities,
- GetMap,
- GetFeatureInfo.

### GetCapabilities

GetCapabilities operation works the same as in case of standard WMS service. The user usually via client application sends GetCapabilities request to the integrating collective service and the service sends a standard XML response, thus the request is not forwarded . Operation GetCapabilities is not sent to any local WMS services. This is shown in Figure 2Figure 2.

![](RackMultipart20210209-4-rdgy8z_html_c50638a396e0b271.png)

_Figure 2 GetCapabilities sequence diagram_

### GetMap

The main difference between a standard WMS service and integrating collective service is backend behaviour of GetMap operation. GetMap in case of proxy service is handled in two ways (scenarios).

To verify which scenario should be applied the proxyAs shown in Figure 3 collective service after receiving initial GetMap request from a client application in the first step checks whether which local services provide data sets for the area defined in a bounding box of the request. covers one or more than one spatial extends of data published by local services. To do this, the proxy collective service uses the databases containing polygon geometry of spatial extends of each connected local service. Then the request is forwarded only to local services that meet selection criteria. The local services send GetMap responses with map pictures covering their respective areas. These responses are captured by collective service and merged creating one map picture. Finally, this flattened picture is returned to the client application as a GetMap response.

![](RackMultipart20210209-4-rdgy8z_html_438f8ba0d7fc5add.png)

_Figure 3 GetMap sequence diagram - basic scenario_

If bounding box of the GetMap request covers an area of spatial data set published by only one local service than specific optimisation can be applied. In such a scenario In the first scenario (one local service) shown in Figure 3, Figure 4 the integrating collective service does not forward the request to the local service but it returns to the client application proper URI pointing to a local service. This is done utilising properties of Hypertext Transfer Protocol (HTTP). Integrating Collective service sends a response with the 302 status code. Then client application sends a new request to the local URI returned by proxy collective service. Finally, local service sends appropriate GetMap response. If the request is correct the response is a picture with visualisation of a data set for requested spatial extent. It is important to mentionmentioning that the whole process of redirecting the request is handled by the server and client application in the background and as such is transparent from the perspective of the end-user. Redirecting a request to a proper local service also reduces to minimum hardware resources utilised by integrating collective service.

![](RackMultipart20210209-4-rdgy8z_html_21e89951cba75f9b.png)

_Figure 4 GetMap sequence diagram - one local service optimised scenario_

In the second scenario (one local service) shown in Figure 4, ![](RackMultipart20210209-4-rdgy8z_html_21e89951cba75f9b.png) the request from the client is forwarded to all local services that publish the data covering the bounding box of the initial request. The local services send a GetMap response with map pictures covering their respective areas. These responses are captured by integrating service and merged creating one map picture. This flattened picture is sent to the client application as a GetMap response.

_Figure 53 GetMap sequence diagram - one local service scenario_

 ![](RackMultipart20210209-4-rdgy8z_html_438f8ba0d7fc5add.png)

_Figure 4 GetMap sequence diagram - many local services scenario_

One of the very few drawbacks of the integrating collective services is additional time needed to process, redirect and forward the requests and responses by additional integratingthe services. However, if the proxy service uses appropriate hardware resources and is configured properly optimally, in practice such time penalty is measured in tens of milliseconds and thus is negligible froorm the end-user perspective.

### GetFeatueInfo

Handling of GetFeatureInfo requests and responses is similar to those of GetMap. Again, two scenarios are applied. The main difference is that the GetFeatureInfo responses are usually returned in HTML or XML format. GetFeatureInfo request is related to a particular pixel of a map, which is almost always covered by only local service. As a result, when the second scenario is applied (many local services) responses of the local services are not merged, but the non-empty response is selected by the proxy collective service and forwarded to a client.

# Standardisation of local services

To facilitate the operation of integrating collective service the local WMS services should be standardised ideally in the following aspects:

- Supported Coordinate Reference Systems (CRSs)
- Names of layers
- Portrayal of layers
- Structure of GetFeatureInfo responses

This can be achieved by developing a standardisation document addressing requirements in the above-mentioned areas. Additionally, the fulfilment of many standardisation requirements can be checked automatically. Therefore, to facilitate the development of local WMS services validation service can be created to validate local services against requirements defined in the standardisation document. In Poland, we have such a document developed for various data published with the use of collective services.

### CRSs

A local service **must** support at least the same CRSs as aan integrated collective service. To avoid any misunderstandings requirements regarding CRSs should be defined utilising their unique [EPSG](https://epsg.io/) codes.

### Names of layer

All local services connected to a proxy service **should** use the same names of layers. If it is beneficial to a local community, local services may publish more layers than required. However, the additional layers are visible only when a user is connected directly to a local service.

If the names of layers are not standardised the integrating collective service when redirecting or forwarding client request to a local service can change the names of layers to those that are supported by a local service. Nonetheless, the standardisation of the layer&#39;s names on the local service level is the recommended approach.

### Portrayal of layers

The local services **should** apply the same portrayal rules for all layers published by integrating collective services.

If the names of layers are not standardised the integrating collective service when redirecting or forwarding client request can apply optional SLD WMS parameter containing the reference to a file with standardised portrayal rules. However, it is recommended to standardise the portrayal rules of local services.

### Structure of GetFeatureInfo responses

The local services **should** provide GetFeatureInfo responses with the standardised content. This allows improving the end-user experience as responses returned by different local services via proxy collective service look the same.

For HTM format the standardisation can be achieved by developing an example of reference HTML response. In case of XML format additionally, XSD application schema can be developed to validate GetFeatureInfo XML responses against the schema.

Again, if GetFeatureInfo responses of local services do not follow the standardised, they can be transformed by the proxy collective service to a common standard by applying for example Extensible Stylesheet Language Transformations (XSLT). Nonetheless, the standardisation of the GetFeatureInfo responses on a local service level is the recommended approach.

# Examples of implementations

Integrating, proxyCollective services have been developed in Poland. The services integrate data sets maintained at a local level by 380 districts counties (powiaty). There are following integrating collective services available:

- KIEG – providing cadastral data (cadastral parcels and buildings) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaEwidencjiGruntow](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaEwidencjiGruntow)
- KIUT- providing data about utilities (electricity, water, telecommunication, sewers, gas and other networks) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaUzbrojeniaTerenu](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaUzbrojeniaTerenu)
- KIBDOT – providing high scale topographic data (containing the location of fences, trees, curbs, etc. ) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaBazDanychObiektowTopograficznych](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaBazDanychObiektowTopograficznych)

![](RackMultipart20210209-4-rdgy8z_html_1d56211924b930f4.gif)

_Figure 64Collective service scheme for cadastral data in Poland (KIEG)_

The integrating collective services have been created utilising mainly Open Source software with additional custom scripts.

To homogenise portrayal and content of integrated collective network service standards standardisation documents have been created and endorsed by GUGiK and all major companies developing the software that helps local governments in Poland maintain the spatial data. The standards are available for [KIEG](http://www.gugik.gov.pl/__data/assets/pdf_file/0006/95046/Specyfikacja-powiatowych-uslug-WMS-z-EGiB-2.2.pdf) and [KIUT](http://www.gugik.gov.pl/__data/assets/pdf_file/0018/211815/Specyfikacja-uslug-publikacji-sieci-uzbrojenia-terenu-1.3.pdf). Additionally, there is [a web application](https://integracja.gugik.gov.pl/walidator/) available for validating KIEG and KIUT services published by districts counties against abovementioned standards.

## Technical solution

The backbone of the Polish solution is [MapServer](https://mapserver.org/). MapServer is an Open Source software that has the ability to act as a WMS Server as well as a WMS Client. The WMS Client capabilities are accessed by defining WMS layers that connect to WMS servers. Detailed documentation on this can be found in [MapServer Documentation](https://www.mapserver.org/ogc/wms_client.html).

# Final words

This document focuses on the development of integrating collective services with WMS interface. A similar approach can be utilised to develop a download Web Feature Service (WFS) or Web Coverage Service (WCS). However, due to the much higher complexity and heterogeneity of WFS and WCS service responses such implementation would require much additional effort to standardise and automatically integrate responses of local services.