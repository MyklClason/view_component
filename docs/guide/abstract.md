---
layout: default
title: Abstract
parent: How-to guide
---

# Abstract

Abstract allow for explictly defining Abstract ViewComponents that are intended to be abstract classes (abstract components). The explict definition is there to make it clear that it is an abstract ViewComponent and in what way is it abstract.

Rendering an abstract ViewComponent will result in an error.

```
class ApplicationComponent < ViewComponent::Base
  abstract
end
```

`abstract` here specifies that the ViewComponent cannot be rendered and can only be subclassed. 

Abstract ViewComponents are split into two general categories that give you more control.

## Templateless ViewComponents
A templateless abstract ViewComponent is as the name implies, has no template. 

By defining a templateless abstract ViewComponent, it will error if a template is provided.

### Examples
You can specify this via one of the following:

```
class ApplicationComponent < ViewComponent::Base
  abstract :missing_template
end
```

## Missing Methods

A missing methods abstract ViewComponent is one where one or more methods required to render the component are missing. These are intended to be defined by the subclass. 

By defining a missing methods abstract ViewComponent it will error if the missing method isn't actually missing.

### Examples
You can specify that a ViewComponent is a missing methods component via one of the following:

```
class DesignSystem::Record::LinkToButton < Record::BaseComponent
  abstract missing_methods: %i(link_to_method)
  erb_template <<-ERB
    <%= link_to_method %>
  ERB
end
```

A subclass might look like:

class Record::LinkToButton::DeleteButtonComponent < DesignSystem::Record::LinkToButton
  def data
    {turbo_method: :delete, turbo_confirm: turbo_confirm_text}
  end
  
  def link_to_class
    "btn btn-danger btn-sm"
  end
  
  def link_to_text
    "Delete"
  end

  def turbo_confirm_text
    "Are you sure?"
  end

  def link_to_method
    link_to link_to_text, record, class: link_to_class, data:
  end
end
