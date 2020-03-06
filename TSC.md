# `typescript` [配置篇](https://www.cnblogs.com/cczlovexw/p/11527708.html)  

## `baseUrl` & `paths`

> A non-relative import can be resolved relative to `baseUrl`, or through path mapping.  
> All module imports with non-relative names are assumed to be relative to the `baseUrl`.
> Note that relative module imports are not impacted by setting the `baseUrl`, as they are always resolved relative to their importing files.  

```json
projectRoot
├── folder1
│   ├── file1.ts (imports 'folder1/file2' and 'folder2/file3')
│   └── file2.ts
├── generated
│   ├── folder1
│   └── folder2
│       └── file3.ts
└── tsconfig.json
```

> The corresponding tsconfig.json would look like:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": [
        "*",
        "generated/*"
      ]
    }
  }
}
```
