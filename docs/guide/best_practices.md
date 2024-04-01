---
layout: default
title: Best Practices
parent: How-to guide
---

# Best Practices

## Everything is a ViewComponent
All Rails rendered HTML becomes ViewComponents.

## Types of ViewComponents

Abstract components when used as a mental model allow for a wider variety of components.

### Top Level Components (TLC)

TLCs are as their name applies, define the top level of the ViewComponent class. Only they directly inherent from `ViewComponent::Base`. They are always Abstract Components with missing templates.

```
class ApplicationComponent < ViewComponent::Base
  abstract :missing_template
end
```


#### Key Points

- TLCs are the only ViewComponent that should inherit directly from `ViewComponent::Base`.
- TLCs are always missing a template.

### Design System Components (DSC)

DSCs are components intended to be used as part of the design system. They are intended to be highly reusable not just across this app, but other Rails apps as well.

Publically available examples of DSC's include:

- GitHub's [Primer ViewComponents](https://primer.style/view-components/)
- [Polaris ViewComponents](https://github.com/baoagency/polaris_view_components)

#### Key Points
DSCs should ideally be usable with any Rails app. To facilitate this:

Standard DSC (SDSC)

- SDSCs should be usable with any Rails app, not just your app.
- SDSCs can be abstract components.
- SDSCs only take in low level data types (arrays/hashes/strings/numbers/etc).

Abstract DSC (ADSC)
- Same rules as above except...
- Can take in higher level data types (records, PORO instances)
- These are intended to be subclassed into PBCs.

### Base Components (BCs)

#### Key Points

- A base component should take in only a single input aside form blocks and data based via dry-effects.
- If a more complex input is needed consider adding a Model or PORO Class that has access to all the inputs needed.
- Structurally, there should be at most 1 BC per PBC component folder as it is the BC for that folder.
- Base Components should be named based on the input. IE: `User::BaseComponent` or `Users::BaseComponent`.
- BCs are also a good place to store methods for manipulating the input to avoid duplication across PBCs that use that BC.
- Organizing VCs by BC allows decreases the likihood of creating redundent VCs.


### Purpose Built Components (PBCs)


#### Key Points

- Should only serve a single purpose.
- Are the only VCs that should have turbo frames in the template.
- Naming Convention: Input::Domain::Purpose. Inputs may have multiple namespaces.

## Database Queries

- Ideally, there should be no DB queries in the VCs.
- Only PBCs and in some cases ACs are permitted be making Database Queries.
- Care should be used to ensure that if DB queries are used, that they will not duplicate queries and will be efficent queries.
- A reason to allow DB Queries in VCs: HTTP Caching.
- DB queries should only be permitted if the input is a Record or a DB Query.

## General Concepts

- It should be quick to add a new VC if needed.
- It should be preferrible to create a new PBC rather than radically edit an existing one. This is especially the case if you are optimizing. IE: `User::AvatarComponent` => `Profile::AvatarComponent` => `Profile::SmallAvatarComponent` => `Avatar::SmallComponent` this allows for keeping the old version for comparision testing. Once no longer needed the old VC can be removed.

## Turbo Frames (TF)

- Only PBC should be have Turbo Frames in the Components.
- Generally speaking, each PBC template should have one or more Turbo Frames.
- Outer TF are used to make replacements easier. Inner most TF references that specific component.

IE: 

```
<%# User::DetailsComponent Template %>
<%= turbo_frame :user-details %>
  <%# User details component %>
<% end %>
```

```
<%# User::DetailsFormComponent Template %>
<%= turbo_frame :user-details %>
  <%= turbo_frame :user-details-form %>
    <%# User details component %>
  <% end %>
<% end %>
```

In this setup is very easy to swap between rendering the user-details and the user-details-form.

## Turbo Stream

- As an optimization, you can respond with a PBC instead of the view that contains it.
- As a further optimization, you can setup a VC that takes in multiple PBCs and renders them for more complex stuff. Useful for broadcasts.
- Do not use the above optimizations unless they are actually needed as they add complexity to the app that is not in general needed.

## dry-effects

- The dry-effects gem provides a rather nice solution to the case where you need to pass config/settings/state information to a ViewComponent. Such as `current_user`. 
- Dry-effects should be used sparingly. For example, passing HTTP params to your VC is not a good reason to use dry-effects.
- dry-effects should be used to pass data that is irrelevant to what is being rendered. IE: A Nav could use dry-effects to get the current user. Which would then be passed onto 
- dry-effects should only be included for the VCs that actually need it.

### Good reasons to use dry-effects

- Passing `current_user` to PBCs that have an input other than the current_user or a user that would be the current user.
- Passing state information to a PBC.

### Bad reasons to use dry-effects

- Passing HTTP params (these should be handled in the controller)

# WIP Notes
This is intended as a way to split out "Best Practices" from the "ViewComponents At Github". The idea is that the community can suggest best practices and GitHub can weigh in on their opinion on the practice.
