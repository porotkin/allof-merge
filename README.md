# allof-merge
<img alt="npm" src="https://img.shields.io/npm/v/allof-merge"> <img alt="npm" src="https://img.shields.io/npm/dm/allof-merge?label=npm"> ![GitHub Workflow Status (with event)](https://img.shields.io/github/actions/workflow/status/udamir/allof-merge/ci.yml)
 <img alt="npm type definitions" src="https://img.shields.io/npm/types/allof-merge"> ![Coveralls branch](https://img.shields.io/coverallsCoverage/github/udamir/allof-merge) <img alt="GitHub" src="https://img.shields.io/github/license/udamir/allof-merge">

Merge schemas combined using allOf into a more readable composed schema free from allOf.

## Features
- Safe merging of schemas combined with allOf in whole JsonSchema based document
- Fastest implmentation - up to x4 times faster then other popular libraries
- Merged schema does not validate more or less than the original schema
- Removes almost all logical impossibilities
- Correctly merge additionalProperties, patternProperties and properties taking into account common validations
- Correctly merge items and additionalItems taking into account common validations
- Supports rules extension to merge other document types and JsonSchema versions
- Supports $refs and circular references either (internal references only)
- Correctly merge of sibling content with $refs (optionally)
- Typescript syntax support out of the box
- No dependencies, can be used in nodejs or browser

## Works perfectly with specifications:

- [JsonSchema](https://json-schema.org/draft/2020-12/json-schema-core.html)
- [OpenApi 3.x](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.3.md)
- ~~Swagger 2.x~~ (roadmap)
- ~~AsyncApi 2.x~~ (roadmap)

## Other libraries
There are some libraries that can merge schemas combined with allOf. One of the most popular is [mokkabonna/json-schema-merge-allof](https://www.npmjs.com/package/json-schema-merge-allof), but it has some limitatons: Does not support circular $refs and no Typescript syntax out of the box.

## External $ref
If schema contains an external $ref, you should bundle it via [api-ref-bundler](https://github.com/udamir/api-ref-bundler) first.

## Installation
```SH
npm install allof-merge --save
```

## Usage

### Nodejs
```ts
import { merge } from 'allof-merge'

const data = {
  type: ['object', 'null'],
  additionalProperties: {
    type: 'string',
    minLength: 5
  },
  allOf: [{
    type: ['array', 'object'],
    additionalProperties: {
      type: 'string',
      minLength: 10,
      maxLength: 20
    }
  }]
}

const onMergeError = (msg) => {
  throw new Error(msg)
}

const merged = merge(data, { onMergeError })

console.log(merged)
// {
//   type: 'object',
//   additionalProperties: {
//     type: 'string',
//     minLength: 10,
//     maxLength: 20
//   }
// }

```

### Browsers

A browser version of `allof-merge` is also available via CDN:
```html
<script src="https://cdn.jsdelivr.net/npm/allof-merge@latest/browser/allof-merge.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/allof-merge@latest/browser/allof-merge.js"></script>
```

Reference `allof-merge.min.js` in your HTML and use the global variable `AllOfMerge`.
```HTML
<script>
  var merged = AllOfMerge.merge({ /* ... */ })
</script>
```

## Documentation

### `merge(data: any, options?: MergeOptions)`
Create a copy of `data` with merged allOf schemas:


### Merge options
```ts
interface MergeOptions {
  // source document if merging only part of it
  // (optional) default = data
  source?: any          
  
  // custom merge rules
  // (optional) default = jsonSchemaMergeRules("draft-06")
  rules?: MergeRules    

  // merge $ref and sibling content
  // (optional) default = false
  mergeRefSibling?: boolean  

  // Merge error hook, called on any merge conflicts
  onMergeError?: (message: string, path: JsonPath, values: any[]) => void
}
```

### Supported rules
You can find supported rules in the src/rules directory of this repository:
- `jsonSchemaMergeRules(version: "draft-04" | "draft-06")`
- `openapiMergeRules(version: "3.0.x" | "3.1.x")`

## Benchmark
```
allof-merge x 800 ops/sec ±2.35% (90 runs sampled)
json-schema-merge-allof x 217 ops/sec ±2.03% (86 runs sampled)
Fastest is allof-merge
```

Check yourself:
```SH
npm run benchmark
```

## Contributing
When contributing, keep in mind that it is an objective of `allof-merge` to have no package dependencies. This may change in the future, but for now, no-dependencies.

Please run the unit tests before submitting your PR: `npm test`. Hopefully your PR includes additional unit tests to illustrate your change/modification!

## License

MIT
