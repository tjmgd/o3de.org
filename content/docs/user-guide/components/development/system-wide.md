---
description: ' Learn how to create system components in Open 3D Engine. '
title: Creating System Components
weight: 600
---

{{< preview-migrated >}}

System components are similar to other components in O3DE's component entity framework\. However, instead of creating game entity behavior, they control the behavior of the engine itself\. System components are first\-class elements of the game engine and are included at a deep level early in the initialization process\. For more information, see [System Components](/docs/userguide/modules/system-components.md) in the [AZ Modules](/docs/userguide/modules/s-intro.md) section\.

Like any O3DE [component](/docs/user-guide/engine/components/create-component.md), a system component can provide services and can depend on or require other system component services\. O3DE provides precise control over the order of engine initialization and system dependencies\.

When you author system components, follow the best practices for component authoring\. For example, your system components should use the following:
+ [Working with the Event Bus \(EBus\) system](/docs/user-guide/engine/ebus/_index.md) to expose their interfaces\.
+ [Reflection](/docs/user-guide/engine/components/reflection.md) to serialize and edit settings in the [Advanced Settings dialog of the Project Configurator](/docs/user-guide/editor/add-system-entity.md)\.
+ The same [AZ::Component Functions](/docs/userguide/components/entity-system-create-component#component-entity-system-create-component-az-functions) for activation and deactivation\.

**Important**
Just like game components, system components often provide request and notification buses\. However, because system components are global systems, they should not specify IDs for their buses like game components\. Game developers should be able to call your system's EBuses without having to deal with or know about the system entity that contains all system components\.

**Topics**
+ [Creating a System Component in a Gem](#component-entity-system-pg-creating-system-components-gem)
+ [Making a Component a System Component](#component-entity-system-pg-creating-system-components-defining)

## Creating a System Component in a Gem {#component-entity-system-pg-creating-system-components-gem}

O3DE enables the creation of custom system components through gems and AZ modules\. Gems are a specialization of AZ modules\. For more information, see [Gems and AZ Modules](/docs/user-guide/engine/gems/structure.md)\. Most O3DE games organize their game code in one or more gems\. These gems can contain system components that integrate with the game engine as well as components for use on game entities\.

When you create a system component as part of a gem, note the following requirements:
+ Your gem's `GetRequiredSystemComponents()` function must return the system component\.
+ Your `GemNameBus.h` file goes under the `lumberyard_version\dev\Gems\GemName\Code\Include\GemName\` directory\.
+ Your component source files go under the `lumberyard_version\dev\Gems\GemName\Code\Source\` directory\.

### Example: HttpRequestorSystemComponent {#component-entity-system-pg-creating-system-components-gem-example}

The [HttpRequestor gem](/docs/userguide/http-requestor-gem.md) makes asynchronous HTTP/HTTPS requests and returns data through a callback function that you provide\. The HttpRequestor gem uses the EBus for communication and provides all requests asynchronously\. The `HttpRequestorSystemComponent` is part of the HttpRequestor gem\. You can find the source code files for the gem under the `lumberyard_version\dev\Gems\HttpRequestor\Code\` directory\.

**The HttpRequestor EBus**

The following code shows the EBus implementation for HttpRequestor \(`HttpRequestorBus.h`\)\.

```
...
#include <AzCore/EBus/EBus.h>
#include "HttpTypes.h"

namespace HttpRequestor
{
    class HttpRequestorRequests
        : public AZ::EBusTraits
    {

    public:
        static const AZ::EBusHandlerPolicy HandlerPolicy = AZ::EBusHandlerPolicy::Single;
        static const AZ::EBusAddressPolicy AddressPolicy = AZ::EBusAddressPolicy::Single;

        // Public functions
        virtual void AddRequest(const AZStd::string& URI, Aws::Http::HttpMethod method, const Callback& callback) = 0;
        virtual void AddRequestWithHeaders(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const Callback& callback) = 0;
        virtual void AddRequestWithHeadersAndBody(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const AZStd::string& body, const Callback& callback) = 0;

        virtual void AddTextRequest(const AZStd::string& URI, Aws::Http::HttpMethod method, const TextCallback& callback) = 0;
        virtual void AddTextRequestWithHeaders(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const TextCallback& callback) = 0;
        virtual void AddTextRequestWithHeadersAndBody(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const AZStd::string& body, const TextCallback& callback) = 0;
    };

    using HttpRequestorRequestBus = AZ::EBus<HttpRequestorRequests>;
} // namespace HttpRequestor
```

**The HttpRequestorSystemComponent Class**

The following code shows the `HttpRequestorSystemComponent` class \(`HttpRequestorSystemComponent.h`\)\.

```
...
#include <AzCore/Component/Component.h>
#include <HttpRequestor/HttpRequestorBus.h>
#include "HttpRequestManager.h"

namespace HttpRequestor
>>>>>>> ly1.13:latest/dg/component_entity_system_pg_creating_system_components.xml
{
    class HttpRequestorSystemComponent
        : public AZ::Component
        , protected HttpRequestorRequestBus::Handler
    {
    public:
        AZ_COMPONENT(HttpRequestorSystemComponent, "{CF29468F-1F67-497F-B4FF-C0F123584864}");

        static void Reflect(AZ::ReflectContext* context);

        static void GetProvidedServices(AZ::ComponentDescriptor::DependencyArrayType& provided);
        static void GetIncompatibleServices(AZ::ComponentDescriptor::DependencyArrayType& incompatible);
        static void GetRequiredServices(AZ::ComponentDescriptor::DependencyArrayType& required);
        static void GetDependentServices(AZ::ComponentDescriptor::DependencyArrayType& dependent);

    protected:
        ////////////////////////////////////////////////////////////////////////
        // HttpRequestorRequestBus interface implementation
        ////////////////////////////////////////////////////////////////////////
        void AddRequest(const AZStd::string& URI, Aws::Http::HttpMethod method, const Callback& callback) override;
        void AddRequestWithHeaders(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const Callback& callback) override;
        void AddRequestWithHeadersAndBody(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const AZStd::string& body, const Callback& callback) override;

        void AddTextRequest(const AZStd::string& URI, Aws::Http::HttpMethod method, const TextCallback& callback) override;
        void AddTextRequestWithHeaders(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const TextCallback& callback) override;
        void AddTextRequestWithHeadersAndBody(const AZStd::string& URI, Aws::Http::HttpMethod method, const Headers & headers, const AZStd::string& body, const TextCallback& callback) override;

        ////////////////////////////////////////////////////////////////////////
        // AZ::Component interface implementation
        void Init() override;
        void Activate() override;
        void Deactivate() override;
        ////////////////////////////////////////////////////////////////////////

    private:
        ManagerPtr              m_httpManager;
    };
}
```

## Making a Component a System Component {#component-entity-system-pg-creating-system-components-defining}

After you create the code for your component, add it to your project's system entity to make it a system component\.

**To define a component as a system component**

1. Use the `GetRequiredSystemComponents()` function to add your component to the system entity for your project during application startup\.

   The following example is from `HttpRequestorModule.cpp`\.

   ```
   ...
   #include "HttpRequestor_precompiled.h"
   #include "HttpRequestorSystemComponent.h"
   #include <AzCore/Module/Module.h>

   namespace HttpRequestor
   {
       class HttpRequestorModule
           : public AZ::Module
       {
       public:
           AZ_RTTI(HttpRequestorModule, "{FD411E40-AF83-4F6B-A5A3-F59AB71150BF}", AZ::Module);

           HttpRequestorModule()
               : AZ::Module()
           {
               // Push results of [MyComponent]::CreateDescriptor() into m_descriptors here.
               m_descriptors.insert(m_descriptors.end(), {
                   HttpRequestorSystemComponent::CreateDescriptor(),
               });
           }

           /**
            * Add required SystemComponents to the SystemEntity.
            */
           AZ::ComponentTypeList GetRequiredSystemComponents() const override
           {
               return AZ::ComponentTypeList{
                   azrtti_typeid<HttpRequestorSystemComponent>(),
               };
           }
       };
   }
   ...
   ```

1. \(Optional\) Expose the system component to the [System Entity Editor](/docs/userguide/configurator/advanced#configurator-advanced-system-entity-editor)\. This enables game developers to configure the component's properties on a per\-project basis\. To do so, reflect the system component to the `EditContext` and set the `AppearsInAddComponentMenu` field to `System`\.

   The following example is from `HttpRequestorSystemComponent.cpp`\.

   ```
   ...
   #include "HttpRequestor_precompiled.h"

   #include <AzCore/Serialization/SerializeContext.h>
   #include <AzCore/Serialization/EditContext.h>

   #include "HttpRequestorSystemComponent.h"

   namespace HttpRequestor
   {
   ...
       void HttpRequestorSystemComponent::Reflect(AZ::ReflectContext* context)
       {
           if (AZ::SerializeContext* serialize = azrtti_cast<AZ::SerializeContext*>(context))
           {
               serialize->Class<HttpRequestorSystemComponent>()
                   ->Version(1)
                   ;

               if (AZ::EditContext* ec = serialize->GetEditContext())
               {
                   ec->Class<HttpRequestorSystemComponent>("HttpRequestor", "Will make HTTP Rest calls")
                       ->ClassElement(AZ::Edit::ClassElements::EditorData, "")
                           // ->Attribute(AZ::Edit::Attributes::Category, "") Set a category
                           ->Attribute(AZ::Edit::Attributes::AppearsInAddComponentMenu, AZ_CRC("System"))
                           ->Attribute(AZ::Edit::Attributes::AutoExpand, true)
                       ;
               }
           }
       }
   ...
   }
   ```

   The `"HttpRequestor"` and `"Will make HTTP Rest calls"` string parameters specify the UI name and tooltip information for the component in the **Add Component** list\. The `Category` field specifies the group in which the component appears\. In this case, no category is specified, so the group is **Miscellaneous** by default\.

   The following image shows the result in the System Entity Editor\.
![\[The HttpRequestor system component appears in the System Entity Editor Add Component menu\]](/images/user-guide/component/entity_system/component-entity-system-pg-creating-system-components-system-editor.png)

   For detailed steps on using the System Entity Editor to add a system component to a project, see [Configuring System Entities](/docs/user-guide/editor/add-system-entity.md)\.