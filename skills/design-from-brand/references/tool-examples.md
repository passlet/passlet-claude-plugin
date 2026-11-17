# Focused design-tool examples

These are tool-specific input fragments, not a complete design recipe. Template
edits also need the real `template_id` and latest `revision`; image uploads do
not. Read supported slot keys, faces, capacity and image roles from
`get_design_options` with `detail: "summary"` when needed. After every successful
template edit, use its new revision in the next edit to that template.

## Define and display a holder value

`add_design_variables` defines a value that differs between issued passes:

```json
{
  "variables": [
    { "key": "memberName", "type": "string", "preview": "Mira Keller" },
    { "key": "memberNumber", "type": "string", "preview": "001204" }
  ]
}
```

Omit issuance `default` values for fictional examples. Bind the information where
it appears. For an Apple layout whose options include `primaryField`,
`update_apple_content` can set:

```json
{
  "slots": {
    "primaryField": { "label": "MEMBER", "value": { "variable": "memberName" } }
  },
  "poster_overrides": {
    "primaryField": { "value": null }
  }
}
```

The override clear applies only to an enabled poster design and makes its value
inherit the shared field. Use it when replacing an old fixed sample override;
omit `poster_overrides` when no override needs changing. The layout choice still
needs a real preview: a long name may work better in another supported slot.

For Google, `update_google_content` uses explicit card properties:

```json
{
  "card_title": "ASTER STUDIO",
  "header": "Membership",
  "rows": [
    [{ "header": "Member", "body": { "variable": "memberName" } }],
    [{ "header": "Number", "body": { "variable": "memberNumber" } }]
  ]
}
```

This replaces every Google detail row. On an existing draft include every row
and binding to keep. The word `Member` is a shared label; `memberName` is the
variable key. Neither `"Mira Keller"` nor `"{{memberName}}"` is a holder binding.

## Make a narrow visual change

For a Google subheader removal, send only `{"subheader": null}` to
`update_google_content`. Omitted header, rows, images, links and barcode remain
unchanged. `{"rows": []}` would instead remove all detail rows; it does not mean
"leave rows alone."

For an Apple label change, send only the changed slot part, for example
`{"slots":{"primaryField":{"label":"NAME"}}}`. Its value and binding remain.
Setting the whole slot to `null` removes the slot, its texts and bindings.

Use `set_design_colors` for palette changes, with properties such as
`{"apple":{"background":"#F4EBDD","foreground":"#252525","label":"#252525"},"google":{"background":"#F4EBDD"}}`.
Use `set_design_images` for placement, for example
`{"images":[{"role":"artwork","asset_id":"<uploaded asset id>"}]}`.
The asset id must come from `upload_design_image` or the saved draft, never a
filename or an invented id. Enable the poster first with
`set_apple_layout` and `{"poster":true}`.

## Prepare images for their roles

The role's `fit` describes an existing file-preparation step, not how Wallet
displays the finished image. Its `preparation.fit` recommends a starting choice
for a new variant. Use `contain` to preserve a complete logo and `cover` to fill
an artwork canvas. Inspect the placed result for display cropping and masks.

For a Google logo with clear space on this example brand's cream background,
`upload_design_image` can reuse an existing source:

```json
{
  "source": { "asset_id": "<existing source asset id>" },
  "role": "googleLogo",
  "preparation": { "fit": "contain", "padding": 0.1, "background": "#F4EBDD" }
}
```

`padding` is a fraction of the shorter output side on each edge, not a number of
pixels or a guarantee that any wordmark fits the circular mask. Choose it for
the supplied mark and inspect the preview. A compact monogram may work better
than a wide wordmark. Google-role copies are public; the original is unchanged.

For photographic poster artwork whose subject is toward the top:

```json
{
  "source": { "url": "https://example.org/approved-artwork.jpg" },
  "role": "artwork",
  "preparation": { "fit": "cover", "crop_position": "top" }
}
```

Use the actual approved image URL. Dimensions come from the role registry; an
Apple `strip` also needs `apple_style` because its canvas varies by style.
Padding/background are contain settings; crop position is a cover setting.
Omitting `preparation` preserves the existing import behavior. Assign the new
returned `id` using `set_design_images`; do not reupload the original bytes.

## Read the result and inspect images

A saved write returns the revision for the next write and current design issues.
An issue can describe a temporary incomplete draft: add the missing content and
check again. Read back the template to verify bindings after a content change.
Then call `preview_design` separately for the Apple poster, classic fallback,
and Google faces that are enabled. Use `variables` overrides for a second holder
and plausible long values. A valid schema or an empty issue list does not verify
visual quality.
