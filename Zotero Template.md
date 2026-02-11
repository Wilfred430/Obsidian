---
citekey: {{citekey}}
status: #reading
tags: [literature-note, {{publicationTags}}]
---

# {{title}}

**Authors:** {{authors}}
**Year:** {{date | format("YYYY")}}
**Journal:** {{publicationTitle}}
**Zotero Link:** [Open in Zotero]({{zoteroSelectURI}})

## Abstract
{{abstractNote}}

---
## Zotero Notes & Highlights

{% for annotation in annotations -%}
{%- if annotation.annotatedText -%}
> {{annotation.annotatedText}} (p. {{annotation.pageLabel}})
{%- endif %}
{%- if annotation.imageRelativePath -%}
![[{{annotation.imageRelativePath}}]]
{%- endif %}
{% if annotation.comment %}
**My Note:** {{annotation.comment}}
{% endif %}
---
{% endfor -%}