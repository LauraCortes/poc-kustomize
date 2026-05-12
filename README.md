# Kustomize JSON Patch Operations

In Kustomize, patches can be defined using the **JSON Patch** standard (RFC 6902), even though they are written in YAML format. The `op` field stands for **operation**.

## What is `op: replace`?

The `replace` operation instructs Kustomize to find the exact location specified in the `path` and overwrite its existing value with the new `value` you provide. 

For example, in `overlays/us-west-2/patches/sizing-patch.yaml`:
```yaml
- op: replace
  path: /spec/template/spec/containers/0/resources/requests/memory
  value: "64Mi"
```
Kustomize looks at the base deployment and overrides the memory requests (`/spec/template/spec/containers/0/resources/requests/memory`) of the first container (index `0`), changing it from its original value to `"64Mi"`. If the path you are trying to replace doesn't exist in the base resource, the operation will fail.

## What other options (operations) exist?

According to the JSON Patch specification, there are 6 standard operations you can use:

### 1. `add`
Adds a new value to an object or inserts a new element into an array. If you use it on an array, you can append to the end by using a hyphen (`-`) in the path.
```yaml
- op: add
  path: /spec/template/spec/containers/0/env/-
  value: 
    name: NEW_VAR
    value: "new_value"
```

### 2. `remove`
Deletes the key/value or array element at the specified path. It does not require a `value` field.
```yaml
- op: remove
  path: /spec/template/spec/containers/0/resources/limits/cpu
```

### 3. `move`
Moves a value from one location to another. It removes the value from the `from` path and adds it to the target `path`.
```yaml
- op: move
  from: /spec/template/spec/containers/0/env/0
  path: /spec/template/spec/containers/0/env/1
```

### 4. `copy`
Copies a value from a specified `from` path to a target `path`, leaving the original value intact.
```yaml
- op: copy
  from: /spec/replicas
  path: /metadata/annotations/original-replicas
```

### 5. `test`
Checks if the value at the specified `path` matches the provided `value`. If the test fails, the entire patch fails and aborts. This is useful as a safeguard to ensure you are modifying the correct configuration.
```yaml
- op: test
  path: /spec/replicas
  value: 3
```

*(Note: The 6th operation is `replace`, which is explained above.)*
