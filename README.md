# sub2api-4k-image-generator-ts

TypeScript-only local skill for generating and debugging high-resolution AI images through a sub2api OpenAI-compatible endpoint.

## Structure

```text
sub2api-4k-image-generator-ts/
├── SKILL.md
├── README.md
├── package.json
├── tsconfig.json
├── scripts/
│   ├── openai_4k_image_generator.ts # thin executable entrypoint
│   ├── cli.ts                      # command orchestration
│   ├── args.ts                     # CLI parsing and defaults
│   ├── env.ts                      # .env loading and key masking
│   ├── payload.ts                  # /images and /responses payload builders
│   ├── request.ts                  # JSON and SSE HTTP requests
│   ├── sse.ts                      # OpenAI image stream event parsing
│   ├── save.ts                     # final and partial image saving
│   ├── types.ts                    # shared types
│   ├── errors.ts                   # typed CLI errors
│   └── test.ts                     # local unit tests
└── dist/
    └── openai_4k_image_generator.js
```

## Setup

Create or update `.env` where you run the command:

```env
SUB2API_BASE_URL=https://sub2api.online
SUB2API_API_KEY=sk-...
```

Install dependencies for development:

```powershell
npm install
```

## Run

Preferred compiled JavaScript:

```powershell
node .\dist\openai_4k_image_generator.js `
  "cinematic xianxia sword immortal, sea of clouds, glowing sword, no text, no watermark" `
  --size 3840x2160 `
  --quality high `
  --output-dir output
```

TypeScript source:

```powershell
npx tsx .\scripts\openai_4k_image_generator.ts `
  "cinematic xianxia sword immortal, sea of clouds, glowing sword, no text, no watermark" `
  --size 3840x2160 `
  --quality high `
  --output-dir output
```

Dry run:

```powershell
npm run dry-run
```

## Useful Options

- `--size 3840x2160` for landscape 4K
- `--size 2160x3840` for portrait 4K
- `--endpoint images` for `/v1/images/generations`
- `--endpoint responses` for `/v1/responses`
- `--input-image ./input.png` with `--endpoint responses`
- `--no-stream` to debug non-streaming behavior
- `--dry-run` to print URL and JSON body without sending

## Diagnostics

- `1010`: Cloudflare blocked the request before it reached sub2api. Add or adjust WAF Skip rules for `/v1/*`.
- `524`: The request reached the origin but did not complete fast enough. Keep streaming enabled and check server-side keepalive behavior.
- `partial image received`: The stream is alive and upstream generation started.
- `*-partial-XX.png`: Partial diagnostic image. It is not the final 4K output.
- `<prefix>-XX.png`: Final saved image.

## Development

```powershell
npm test
npm run build
```

The tests cover argument defaults, payload construction, URL selection, final image SSE extraction, partial image SSE extraction, response summaries, and API key masking.
