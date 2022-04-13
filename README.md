# Web Component Korben

This is a project that enables to create a Korben RSS web component 

## Get started

Install the dependencies...

```bash
npm install
```

## Developing and preview the web component

Add the src/Korben.svelte
Import Korben.svelte in main js
Add customElement:true to compilerOption in rollup.config.js

```bash
npm run dev
```

## Building the web component

```bash
npm run build-component
```

## Use the web component

copy Korben.js from public/build and use it like this:

<head>
    <title>Test Korben korben-rss-feed</title>
    <script src="../public/build/Korben.js"></script>
</head>
<body>
    <div class="container">
        <div class="row">
            <div class="col">
                <korben-rss-feed card_title="Articles Korben">
                </korben-rss-feed>
            </div>
        </div>
    </div>
</body>
</html>




