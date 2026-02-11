# {{title}}

**Authors:** {{authors}}
**Year:** {{date | format("YYYY")}}
**Journal:** {{publicationTitle}}
**Zotero Link:** [Open in Zotero]({{zoteroSelectURI}})
{% if fileLink %}**PDF:** [Open PDF]({{fileLink}}){% endif %}

## Abstract
> [!abstract]+
> {{abstractNote}}

---
## Zotero Notes & Highlights

{% for annotation in annotations -%}
{%- if annotation.annotatedText -%}
> [!quote|{{annotation.colorCategory}}] {{annotation.type | capitalize}} (p. {{annotation.pageLabel}})
> {{annotation.annotatedText}} [Link]({{annotation.desktopURI}})
{%- endif -%}

{%- if annotation.imageRelativePath -%}
![[{{annotation.imageRelativePath}}]]
{%- endif -%}

{%- if annotation.comment -%}
> [!note] My Comment
> {{annotation.comment}}
{%- endif -%}
---
{% endfor -%}
