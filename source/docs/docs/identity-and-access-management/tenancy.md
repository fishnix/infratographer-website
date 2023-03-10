# Tenancy

## Scope

Tenancy is the concept of providing isolated access to resources. In the context of the Infratographer ecosystem, we are talking about the isolation of data and the ability to provide a single application to multiple tenants. The scope of this document is to provide a shared understanding and a working model for developing multi-tenant applications within the Infratographer ecosystem. It is outside the scope to discuss multi-tenancy in other systems beyond Infratographer except where drawing comparison helps to clarify our intent.

## Principles of Tenancy

### Tenant Trees are Isolated

Tenants provide isolated access to data. Access to data within a tenant tree needs to be explicitly granted.

#### Rationale

Tenant isolation limits access privileges to data and control over resources managed by the ecosystem.

#### Implications

Access to a parent tenant is not implied by granting access to a child tenant and access to one tenant does not imply access to other unrelated tenant trees. [Access granted to a parent tenant *does* imply granting access to child tenants](#tenant-permissions-are-inherited)

### Tenant permissions are inherited

Access granted on a tenant will be inherited by all subtenants.

#### Rationale

Inheriting access rights from the parent is more intuitive and fits better into existing authorization paradigms.

#### Implication

When access is granted to a tenant, that access will be granted to all subtenants of the tenant at the same level. Access cannot be limited to only one level unless the tenant has no children. In some cases, a flat tenant structure may be better serve isolation requirements.

### Tenants are Hierarchical

Tenants form a tree structure, and are not limited to a single level.

#### Rationale

Experience with multiple large projects (Openstack[1], Kubernetes[2], etc) has shown that many ecosystems start with single level tenancy and then migrate to hierarchical tenancy in the future. This migration is often fraught with peril, bugs and dragons. Other systems like GCP provide hierarchical multi-tenancy using tree structures. In GCP the tree structure is very clear and is represented by the organization, folders, and projects [3].

#### Implications

Tenants can have sub-tenants that can have sub-tenants, etc. Recursion needs to be accounted for in services built for the Infratographer ecosystem.

### Services in the ecosystem are Multi-Tenant

Services and applications in the Infratographer ecosystem will need to interact with multiple tenants. This will be done securely and with awareness.

#### Rationale

To support multitenancy, ecosystem services need to be tenant aware or have an instance deployed for every tenant. The former is preferable to the later.

#### Implications

The services that form the Infratographer ecosystem are aware of the tenants and provide the means of segmenting resources owned by those tenants. This allows for a single application to be provided to multiple tenants. Resource segmentation must be explicit.

### Resources are owned by Tenants

All resources are owned by a single tenant, not multiple tenants.

#### Rationale

Granular access control will provide for a more secure environment. Identities can be given access to individual resources or tenants. Tracking an arbitrary number of tenants for a resource adds unecessary complexity.

#### Implications

Services in the Infratographer ecosystem must track a single tenant association for each resource they manage. Tenants and subtenants have no differences other than subtenants have a parent tenant. Drawing a distinction between these entities leads to complications when needing to apply "global" (or at least for a whole tree) authorization constructs and quota management

## References

Much of the inspiration for this doc came the documentation written in [this repo](https://github.com/infratographer/fertilesoil/tree/main/docs).

[1] https://youtu.be/KvKiAzKSVYs
[2] https://youtu.be/j5x6NumP21c
[3] https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy
