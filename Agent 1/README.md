A complete, production-ready README-style Markdown script is below, summarizing what the workflow does, how it’s organized, how to run it, and how each node connects across the three main paths: Markdown Docs, Drive Files, and Image Generation, including model usage and Google Sheets status updates.[1]

### Overview
This n8n workflow automates end-to-end generation of Sub-Industry AI pages by reading structured rows from Google Sheets, creating clean publication-ready Markdown via LLMs, storing the content in Google Docs and as .md files in Google Drive, generating a hero image from an image prompt, and updating status columns back in the source sheet for full traceability and rerun control.[1]

### Key Capabilities
- Ingests rows from the “Supply_Chain” sheet of a Google Sheets document and filters only items not yet processed based on three status columns to avoid duplicates.[1]
- Produces two artifacts per record: a Google Doc populated with the generated Markdown and a .md file uploaded to a Drive folder, both titled with the Subindustry Id for easy indexing.[1]
- Generates a hero banner image using an OpenRouter Gemini image-preview model from a concise image_prompt returned by the LLM, stores it as PNG in Drive, and flags the sheet when the image is generated.[1]

### Data Sources and Destinations
- Source: Google Sheets documentId 1ynj56-BrW2mugvpC7H7D-DVuFVIkGCQ9gO03t8Bi1h4, sheet “Supply_Chain” (gid 282482400).[1]
- Docs Destination: Google Docs folderId 15bZ5C9cGR8eVtnvRqrLMB3wmUZwTeUX_ (document title = row Id).[1]
- Markdown Files Destination: Google Drive folderId 12nlWSGo_33Ss6PSd99em8ZEdxsML48Kb, filename = {Id}.md.[1]
- Images Destination: Google Drive folderId 1AHA3gltqWVho7AnjJ0rhWpVhouIxcjDg, filename = {Id}.png.[1]

### Status Columns (in Google Sheets)
- Markdown format Word Doc Created: set to “Yes” after the Google Doc is created and populated.[1]
- Markdown File Generated: set to “Yes” after the .md file is uploaded to Drive.[1]
- Image Generated: set to “Yes” after the hero image PNG is created and uploaded to Drive.[1]

### High-Level Flow
- Manual Trigger → Google Sheets fetch → IF status filter → Split In Batches iteration per row.[1]
- Markdown artifacts path:
  - LLM JSON generation (markdown + image_prompt) → Parse and clean → Google Doc create + update → Upload .md to Drive → Sheet updates for doc and file.[1]
- Image path:
  - Generate image via OpenRouter Gemini model from image_prompt → Base64 decode → PNG file → Upload PNG → Sheet update for “Image Generated”.[1]
- Optionally, additional chains produce alternative Markdown using GPT-4o-mini or Claude 3.5 via chain nodes for refinement and targeted outputs, also exporting to Docs/Drive as needed.[1]

### LLMs and Model Providers
- Primary structured page generation: Perplexity node produces a JSON with keys markdown and image_prompt using a detailed blueprint prompt with 19 required sections, freshness rule, and reference rules; code nodes robustly parse and normalize output, removing code fences and handling double-stringified JSON edge cases.[1]
- Image generation: OpenRouter POST to google/gemini-2.5-flash-image-preview with the image_prompt text content; pipeline extracts base64 data for binary conversion to PNG.[1]
- Alternate/secondary page generation flows:
  - Chain using @n8n/n8n-nodes-langchain with GPT-4o-mini to convert existing markdown to a complete publication-ready page.[1]
  - Chain using OpenRouter Chat Model with anthropic/claude-3.5-haiku feeding into a Chain LLM block to output markdown-only pages for additional variants.[1]

### Prompts and Output Blueprint
- System prompt for Perplexity: enforces a rigid 19-section page blueprint (Eyebrow + Breadcrumbs through References), machine-readable formatting, embedded KPI cards, data landscape, use case grid, deep dives, implementation blueprint, governance, FAQ, glossary, related pages, CTAs, E-E-A-T author section, and a references section mixing provided URLs with 4–6 plausible/credible sources when web access is limited; also returns a compact image_prompt for hero composition.[1]
- Strict output contract: return only a JSON object with exactly keys markdown and image_prompt to ensure downstream parsing and storage nodes work consistently; robust “safeParse” routines attempt JSON parsing, code-fence stripping, newline normalization, and fallback strategies to avoid pipeline breaks.[1]
- Freshness: mandates “Last updated: {today}” line inside Executive Summary, helping governance and SEO freshness signals for generated pages.[1]

### Node Highlights
- Get row(s) in sheet4: reads rows, credentials via googleSheetsOAuth2Api, and pushes into If6 filter.[1]
- If6: allows only rows where Word Doc, Markdown File, and Image statuses are empty, preventing re-processing.[1]
- Convert into String: stringifies the input JSON record for a consistent LLM content payload.[1]
- Generate data (Perplexity): produces structured JSON with markdown and image_prompt strictly following the blueprint and schema; includes defensive system instructions against hallucination and formatting drift.[1]
- Pass output: safeParse logic cleans and parses LLM content, normalizing keys and stripping code fences, returning clean markdown and image_prompt.[1]
- Create a document + Update a document: creates a Google Doc titled with Id and inserts the full markdown into the document body; then updates “Markdown format Word Doc Created” = Yes in Sheets.[1]
- Convert to Markdown + Upload file: serializes markdown to .md and uploads to Drive folder; then updates “Markdown File Generated” = Yes in Sheets.[1]
- Generate Image → Set base for conversion → Convert Images → Upload Images: calls OpenRouter Gemini image model, extracts base64, converts to PNG, uploads to Drive, and updates “Image Generated” = Yes in Sheets; Wait node throttles to avoid API overlap.[1]
- Secondary chain: “Message a model4” (GPT-4o-mini) and “OpenRouter Chat Model2” (Claude 3.5 Haiku) paths feed into “Basic LLM Chain2” for refined markdown-only outputs, also exported to Docs/Drive via their own Convert/Upload nodes as labeled in the graph.[1]

### File/Folder IDs and Naming
- Docs Folder: 15bZ5C9cGR8eVtnvRqrLMB3wmUZwTeUX_.[1]
- Markdown Folder: 12nlWSGo_33Ss6PSd99em8ZEdxsML48Kb.[1]
- Images Folder: 1AHA3gltqWVho7AnjJ0rhWpVhouIxcjDg.[1]
- Doc titles and filenames: {Id}.md and {Id}.png to align artifacts with sheet rows.[1]

### Error-Handling and Hardening
- JSON parsing: multiple attempts including stripping code fences, removing “json”/“markdown” labels, escaping issues, and double-parse to handle double-stringified payloads; fallback wraps content into a minimal object to keep the pipeline moving.[1]
- Rerun-safety: three status columns gate re-execution to avoid duplicates; Split In Batches ensures per-row isolation, with Wait preventing rate limit conflicts for image generation.[1]
- Missing fields: prompts instruct LLMs to use concise, sensible defaults tailored to SubIndustry while avoiding unverifiable claims; stats must be marked estimate/typical if speculative.[1]

### Prerequisites
- Linked credentials: Google Sheets OAuth2, Google Drive OAuth2, Google Docs OAuth2, Perplexity API, OpenRouter API; ensure all are connected and authorized in n8n Credentials.[1]
- Access: the specified Docs and Drive folder IDs must exist and be accessible; the Google Sheet must contain the expected columns (Id, Industry, Subindustry, References, status columns, and optional fields used by prompts).[1]

### How to Run
- Open the workflow and confirm credential bindings are valid for Sheets/Docs/Drive/Perplexity/OpenRouter; adjust folder IDs and the source sheet if needed.[1]
- Click “Execute workflow” to fetch rows and process only those missing all three statuses; artifacts will be created for each row in the batch and statuses will be updated back into the sheet.[1]
- To rerun specific entries, clear the relevant status columns (e.g., reset “Image Generated” to empty) and execute again; the IF node will pick them up for reprocessing.[1]

### Extensibility
- Content variants: toggle the GPT-4o-mini or Claude Haiku chains for alternative copy styles; connect their outputs to the existing Convert/Upload nodes or parallel destinations.[1]
- Asset pipelines: swap OpenRouter model or post-process images (e.g., resizing, watermarking) by placing nodes between Convert Images and Upload Images.[1]
- Publishing: add a CMS API node after Upload file to publish Markdown to a website; keep Google Doc creation for editorial workflows and approvals.[1]

### Known Limitations
- LLM variability: despite strict prompts and safeParse, malformed JSON can still occur; the current fallback stores raw markdown but may omit image_prompt for that record.[1]
- Reference enrichment: when real URLs are unavailable, the blueprint permits plausible source labels; human editorial pass is recommended before publication to ensure citation integrity.[1]

### Maintenance Notes
- Keep the 19-section blueprint prompts in sync across Perplexity and chain nodes to avoid schema drift; the primary prompt is the canonical source of structure and rules.[1]
- Monitor API quotas for Sheets, Docs, Drive, Perplexity, and OpenRouter; adjust Wait durations or batch sizes if rate limits are hit.[1]
- Version control: export the n8n workflow JSON and store in the repo; document any ID or folder changes in a config section for easier environment replication.[1]

[1](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/95605742/b20f728a-49d1-42fd-a64f-478a3d5606cd/paste.txt)
