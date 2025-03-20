# Services

## Composition

The service layer is content-centric and revolves around _generation_, _serving_, and _consumption_ of AI agent data models.
To facilitate these operations, the service plane is decomposed into layers based on their respective responsibilities:

```{image} ../_static/service_composition.png
:alt: Service Layer Composition
:width: 100%
:align: center
```
<br>

- **Data Layer**
  Tools, services, and processes centered around _generation_ of data models.
  Examples of this include build tools and SDKs used to compile agent data models from given sources such as GitHub repositories.

- **Serving Layer**
  Tools, services, and processes centered around _serving_ of data models.
  Examples of this include storage services and SDKs for publication and retrival of agent data models.

- **Application Layer**
  Tools, services, and processes centered around _consumption_ of data models.
  Examples of this include third-party applications such as search service that rely on agent data model contents to perform or expose certain operations.
