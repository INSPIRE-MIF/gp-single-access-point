Head Office of Geodesy and Cartography

# **Guidance for the integration of dispersed WMS sources**

`Version 1.0.30`

`18th of February 2021`


## Table of contents

* [References](#references)
  * [Normative References](#normativeReferences)
  * [Technical References](#technicalReferences)
* [Terms](#terms)
* [Introduction](#introduction)
* [Utilised standards](#standards)
  * [INSPIRE Directive](#inspireDirective)
  * [Supported operations](#supportedOperations)
    * [GetCapabilities](#getCapabilities)
    * [GetMap](#getMap)
    * [GetFeatueInfo](#getFeatureInfo)
* [Standardisation of local services](#standardisationOfLocalServices)
  * [CRSs](#crss)
  * [Names of layer](#namesOfLayers)
  * [Portrayal of layers](#portrayalOfLayers)
  * [Structure of GetFeatureInfo responses](#structureOfGetfeatureinfoResponses)
* [Examples of implementations](#examplesOfImplementation)
* [Technical solution](#technicalSolutions)
* [Final words](#finalWords)

# References <a name="references"></a> 

This guidance contains references to or derives concepts from the following documents.

## Normative References <a name="normativeReferences"></a> 

INSPIRE, Implementing Directive 2007/2/EC of the European Parliament and of the Council as regards interoperability of spatial data sets and service

INSPIRE, INS NS, Commission Regulation (EC) No 976/2009 of 19 October 2009 implementing Directive 2007/2/EC of the European Parliament and of the Council as regards the Network Services

INSPIRE, INS DS, Commission Regulation (EU) No 1089/2010 of 23 November 2010 implementing Directive 2007/2/EC of the European Parliament and of the Council as regards interoperability of spatial data sets and services

INSPIRE, INS DSTG, Technical Guidance for the implementation of INSPIRE Discovery Services OGC 06-042 OpenGIS Web Map Service (WMS) Implementation Specification

OGC 09-025r2 Web Feature Service 2.0 Interface Standard – With Corrigendum

OGC 05-078r4, OGC SLD, OGC™ Styled Layer Descriptor profile of the Web Map Service Implementation Specification, version 1.1.0 (Release 4) and its corrigendum1 for OGC Implementation Specification SLD 1.1.0 (07-123r1)

OGC 06-121r3 – OGC Web Services Common Specification (OWS) 1.1.0

Izdebski, W. (2017, 22 September). Analysis of the cadastral data published in the Polish Spatial Data Infrastructure. _Polish Academy of Sciences_. Retrieved from http://www.izdebski.edu.pl/kategorie/Publikacje/2017_WaldemarIzdebski_Analysis_of_the_cadastral_data_published_in_the_Polish_SDI.pdf

Izdebski, W. (2017). Analysis of possibilities to increase the accessibility of WMS services from cadastral databases. _Roczniki Geomatyki 2017, tom XV, zeszyt 4(79)_. Retrieved from http://www.izdebski.edu.pl/kategorie/Publikacje/pobierz.php?p=2017_PTIP_Izdebski_AnalizaMozliwosciZwiekszeniaDostepnosciDanychEwidencyjnychIIP.pdf

## Technical References <a name="technicalReferences"></a> 

Technical Guidance for the implementation of INSPIRE View Services, version 3.11, [INS TG WMS+WMTS](https://inspire.ec.europa.eu/documents/technical-guidance-implementation-inspire-view-services-1)

# Terms <a name="terms"></a>

**collective service**

proxy network service working on top and integrating many network services providing the same or similar spatial data sets in terms of data model but with the different spatial extent

**discovery services**

making it possible to search for spatial data sets and services on the basis of the content of the corresponding metadata and to display the content of the metadata [INSPIRE Directive]

**download services**

enabling copies of spatial data sets, or parts of such sets, to be downloaded and, where practicable, accessed directly [INSPIRE Directive]

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

# Introduction <a name="introduction"></a> 

The responsibility for maintenance of spatial data sets differs across European Union (EU) countries. The data set belonging to the same specific INSPIRE thematic category depending on the country can be maintained on the national level, regional and local level. For example, cadastral data sets in countries like Spain or Czechia and kept on the national level, whereas in Poland the data is maintained locally.

Maintaining similar spatial data sets on a regional or local level leads to a dispersion of data sets and hinders users’ access to the spatial data. A user who wants to access and use the data published by many local or regional organisations has to put some additional effort first to find and later integrate data from many data sources. And if there are hundreds of such data sources, it may require many hours of additional work or investing extra resources on integration services provided by specialised third parties.

To avoid that a proxy service can be created, integrating services maintained on local, regional or even national levels. Such proxy service from a user perspective offers one seamless data set for the bigger area. Figure 1 shows the concept of collective service. 


![Concept of the collective service](https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/collectiveserviceconcept.png "Concept of the collective service")

_Figure 1 Concept of the collective service._
<!-- <p align="center">

  <img src="https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/collectiveserviceconcept.png" alt="Concept of the integrating collective service." style="width:100%">
  <p style="text-align: center;">Figure 1 Concept of the integrating collective service.</p>

</p> -->

It is important to mention that while proxy service gives users access to data covering a bigger area, a user can still connect directly to a local service.

INSPIRE discovery services natively utilize the concept of service integration. Catalogue Service for Web (CS-W) standard supports Harvest operation that as its name suggest can harvest metadata records from local catalogue servers to a parent catalogue. Therefore, a user of parent (e.g. national) metadata catalogue has access to the metadata provided by child catalogues (e.g. regional or local). Similarly, users via EU INSPIRE metadata catalogue have access to the content of national catalogues.

However, such an approach is not natively supported in case of INSPIRE view and download services. Therefore, some additional technical solutions have to be implemented to facilitate access to local data.

This document provides guidelines on developing such collective service for view services utilising Web Map Service (WMS) standard.


# Utilised standards <a name="standards"></a> 

The collective service should support WMS standards in at least two last and most popular versions 1.1.1 and 1.3.0. At the time of creating of this document, it seems that the proposed solution can also be applied for new OGC API – Maps standard that is still under development.

## INSPIRE Directive <a name="inspireDirective"></a> 

## Supported operations <a name="supported"></a> 

The proxy service should support at least GetCapabilities and GetMap operations. However, it is recommended that GetFeatureInfo operation is also supported. 

Proxy service from a service’s user perspective works like any other WMS service. However, its backend differs from the backend of typical WMS service. The similarities and differences are explained in the following sections:

- GetCapabilities,
- GetMap,
- GetFeatureInfo.

### GetCapabilities <a name="getCapabilities"></a> 

GetCapabilities operation works the same as in case of standard WMS service. The user usually via client application sends GetCapabilities request to the collective service and the service sends a typical XML response, thus the request is not forwarded to any local WMS service. This is shown in Figure 2.

![GetCapabilities sequence diagram](https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/GetCapabilities.png "GetCapabilities sequence diagram")

_Figure 2 GetCapabilities sequence diagram_

### GetMap <a name="getMap"></a> 

The main difference between a standard WMS service and collective service is backend behaviour of GetMap operation.

As shown in Figure 3, collective service after receiving initial GetMap request from a client application in the first step checks which local services provide data sets for the area defined in a bounding box of the request. To do this, the collective service uses the databases containing polygon geometry with spatial extends of each connected local service. Then the request is forwarded only to local services that meet selection criteria. The local services send GetMap responses with map pictures covering their respective areas. These responses are captured by collective service and merged creating one map picture. Finally, this flattened picture is returned to the client application as a GetMap response.

![GetMap sequence diagram - basic scenario](https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/GetMapManyLocalServices.png "GetMap sequence diagram - basic scenario")

_Figure 3 GetMap sequence diagram - basic scenario_

If bounding box of the GetMap request covers an area of spatial data set published by only one local service than specific optimisation can be applied. In such a scenario shown in Figure 4, the collective service does not forward the request to the local service, but it returns to the client application proper URI pointing directly to a local service. This is done utilising properties of Hypertext Transfer Protocol (HTTP). Collective service sends a response with the 302 status code. Then client application sends a new request to the local URI returned by collective service. Finally, local service sends appropriate GetMap response. If the request is correct, the response is a picture with visualisation of a data set for requested spatial extent. It is important mentioning that the whole process of redirecting the request is handled by the server and client application in the background and as such is transparent from the perspective of the end-user. Redirecting a request to a proper local service also reduces to minimum hardware resources utilised by collective service. 

![GetMap sequence diagram - one local service optimised scenario](https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/GetMapOneLocalService.png "GetMap sequence diagram - one local service optimised scenario")

_Figure 4 GetMap sequence diagram - one local service optimised scenario_

One of the very few drawbacks of the collective services is additional time needed to process, redirect and forward the requests and responses of the services. However, if the proxy service uses appropriate hardware resources, in practice such time penalty is measured in tens of milliseconds and thus is negligible from the end-user perspective.

### GetFeatueInfo <a name="getFeatureInfo"></a> 

Handling of GetFeatureInfo requests and responses is similar to those of GetMap. The main difference is that the GetFeatureInfo responses are usually returned in HTML or XML format. GetFeatureInfo request is related to a particular pixel of a map, which is almost always covered by only one local service. As a result, responses of the local services are not merged, but the non-empty response is selected by the collective service and forwarded to a client.

# Standardisation of local services <a name="standardisationOfLocalServices"></a> 

To facilitate the operation of integrating collective service, the local WMS services should be standardised ideally in the following aspects:

- Coordinate Reference Systems (CRSs)
- Names of layers
- Portrayal of layers
- Structure of GetFeatureInfo responses

This can be achieved by developing a standardisation document addressing requirements in the above-mentioned areas. Additionally, the fulfilment of many standardisation requirements can be checked automatically. Therefore, to facilitate the development of local WMS services validation service can be created to validate local services against requirements defined in the standardisation document. 

### CRSs <a name="crss"></a> 

A local service **must** support at least the same CRSs as a collective service. To avoid any misunderstandings requirements regarding CRSs should be defined utilising their unique [EPSG](https://epsg.io/) codes.

### Names of layer <a name="namesOfLayers"></a> 

All local services connected to a proxy service **should** use the same names of layers. If it is beneficial to a local community, local services may publish more layers than required. However, the additional layers are visible only when a user is connected directly to a local service.

If the names of layers are not standardised the collective service when redirecting or forwarding client request to a local service can change the names of layers to those that are supported by a local service. Nonetheless, the standardisation of the layer’s names on the local service level is the recommended approach.

### Portrayal of layers <a name="portrayalOfLayers"></a> 

The local services **should** apply the same portrayal rules for all layers published by collective services.

If the names of layers are not standardised the collective service when redirecting or forwarding client request can apply optional SLD WMS parameter containing the reference to a file with standardised portrayal rules.  However, it is recommended to standardise the portrayal rules of local services.

### Structure of GetFeatureInfo responses <a name="structureOfGetfeatureinfoResponses"></a> 

The local services **should** provide GetFeatureInfo responses with the standardised content. This allows improves the end-user experience as responses returned by different local services via collective service look the same.

For HTML format the standardisation can be achieved by developing an example of reference HTML response. In case of XML format, XSD application schema can be developed to validate GetFeatureInfo XML responses against the schema.

Again, suppose GetFeatureInfo responses of local services do not follow the standard. In that case, they can be transformed by the collective service to a common structure by applying, for example, Extensible Stylesheet Language Transformations (XSLT). Nonetheless, the standardization of the GetFeatureInfo responses on a local service level is the recommended approach.

# Examples of implementations <a name="examplesOfImplementation"></a> 

Collective services have been developed in Poland. The services integrate data sets maintained at a local level by 380 counties (powiaty). There are following collective services available:

- KIEG – providing cadastral data (cadastral parcels and buildings) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaEwidencjiGruntow](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaEwidencjiGruntow)
- KIUT - providing data about utilities networks (electricity, water, telecommunication, sewers, gas and other) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaUzbrojeniaTerenu](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaUzbrojeniaTerenu)
- KIBDOT – providing large scale topographic data (containing the location of fences, trees, curbs, etc. ) [https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaBazDanychObiektowTopograficznych](https://integracja.gugik.gov.pl/cgi-bin/KrajowaIntegracjaBazDanychObiektowTopograficznych)

![Collective service scheme for cadastral data in Poland (KIEG)](https://raw.githubusercontent.com/marty1357/gp-single-access-point/main/resources/images/collectivecadastre.png "Collective service scheme for cadastral data in Poland (KIEG)")

_Figure 5 Collective service scheme for cadastral data in Poland (KIEG)_

The collective services have been created utilising mainly Open Source software with additional custom scripts.

To homogenise portrayal and content of collective network service standardisation documents have been created and endorsed by GUGiK and all major companies developing the software that helps local governments in Poland maintain the spatial data. The standards are available for [KIEG](http://www.gugik.gov.pl/__data/assets/pdf_file/0006/95046/Specyfikacja-powiatowych-uslug-WMS-z-EGiB-2.2.pdf) and [KIUT](http://www.gugik.gov.pl/__data/assets/pdf_file/0018/211815/Specyfikacja-uslug-publikacji-sieci-uzbrojenia-terenu-1.3.pdf). Additionally, [a web application](https://integracja.gugik.gov.pl/walidator/) is available for validating KIEG and KIUT services published by counties against above-mentioned standards.

## Technical solution <a name="technicalSolutions"></a> 

The backbone of the Polish solution is [MapServer](https://mapserver.org/). MapServer is an Open Source software that can act as a WMS Server as well as a WMS Client. The MapServer allows for use of data from different servers, and enables the creation of a network of WMS services from which clients can build customized maps. Detailed documentation containing information about using MapServer’s WMS connection type to include layers from remote WMS servers can be found on [MapServer website](https://www.mapserver.org/ogc/wms_client.html).

# Final words <a name="finalWords"></a> 

This document focuses on the development of collective services with WMS interface. A similar approach can be utilized to develop a download Web Feature Service (WFS) or Web Coverage Service (WCS). However, due to the much higher complexity and heterogeneity of WFS and WCS service responses, such implementation would require much additional effort to standardize and automatically integrate local services responses.
