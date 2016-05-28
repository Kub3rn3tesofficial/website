{% if concept %}<!-- check for this before going any further; if not present, skip to else at bottom -->

# Overview of {{concept}}s

{% if what_is %}

### What is a {{ concept }}?

{{ what_is }}

{% else %}

### ERROR: You must define a "what_is" block
{: style="color:red" }

This template requires that you explain what this concept is. This explanation will
be displayed under the heading, **What is a {{ concept }}?**

To get rid of this message and take advantage of this template, define the `what_is`
variable and populate it with content.

```liquid
{% raw %}{% capture what_is %}{% endraw %}
A {{ concept }} does x and y and z...(etc, etc, text goes on)
{% raw %}{% endcapture %}{% endraw %}
```
{% endif %}


{% if when_to_use %}

### When to use {{ concept }}s

{{ when_to_use }}

{% else %}

### ERROR: You must define a "when_to_use" block
{: style="color:red" }

This template requires that you explain when to use this object. This explanation will
be displayed under the heading, **When to use {{ concept }}s**

To get rid of this message and take advantage of this template, define the `when_to_use`
variable and populate it with content.

```liquid
{% raw %}{% capture when_to_use %}{% endraw %}
You should use {{ concept }} when...
{% raw %}{% endcapture %}{% endraw %}
```
{% endif %}


{% if when_not_to_use %}

### When not to use {{ concept }}s (alternatives)

{{ when_not_to_use }}

{% else %}

### ERROR: You must define a "when_not_to_use" block
{: style="color:red" }

This template requires that you explain when not to use this object. This explanation will
be displayed under the heading, **When not to use {{ concept }}s (alternatives)**

To get rid of this message and take advantage of this template, define the `when_not_to_use`
block and populate it with content.

```liquid
{% raw %}{% capture when_not_to_use %}{% endraw %}
You should not use {{ concept }} if...
{% raw %}{% endcapture %}{% endraw %}
```
{% endif %}


{% if status %}

### {{ concept }} status

{{ status }}

{% else %}

### ERROR: You must define a "status" block
{: style="color:red" }

This template requires that you explain the current status of support for this object.
This explanation will be displayed under the heading, **{{ concept }} status**.

To get rid of this message and take advantage of this template, define the `status`
block and populate it with content.

```liquid
{% raw %}{% capture status %}{% endraw %}
The current status of {{ concept }}s is...
{% raw %}{% endcapture %}{% endraw %}
```
{% endif %}


{% if required_fields %}

### {{ concept }} spec

#### Required Fields 

{{ required_fields }}

{% else %}

### ERROR: You must define a "required_fields" block
{: style="color:red" }

This template requires that you provide a Markdown list of required fields for this
object. This list will be displayed under the heading **Required Fields**.

To get rid of this message and take advantage of this template, define the `required_fields`
block and populate it with content.

```liquid
{% raw %}{% capture required_fields %}
* `kind`: Always `Pod`.
* `apiVersion`: Currently `v1`.
* `metadata`: An object containing:
    * `name`: Required if `generateName` is not specified. The name of this pod.
      It must be an
      [RFC1035](https://www.ietf.org/rfc/rfc1035.txt) compatible value and be
      unique within the namespace.
{% endcapture %}{% endraw %}
```

**Note**: You can also define a `common_fields` block that will go under a heading
directly underneath **Required Fields** called **Common Fields**, but it is
not required. 
{% endif %}


{% if common_fields %}

#### Common Fields 

{{ common_fields }}

{% endif %}


<!-- continuing the "if concept" if/then: -->

{% else %}

### ERROR: You must define a "concept" variable
{: style="color:red" }

This template requires a variable called `concept` that is simply the name of the
concept for which you are giving an overview. This will be displayed in the 
headings for the document.

To get rid of this message and take advantage of this template, define `concept`:

```liquid
{% raw %}{% assign concept="Replication Controller" %}{% endraw %}
```

Complete this task, then we'll walk you through preparing the rest of the document.

{% endif %}
