# ComfyUI Node Implementation Patterns

## Node Categories By Function

### 1. Model Management Nodes
Example: `CheckpointLoaderSimple`
```python
class CheckpointLoaderSimple:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": { 
                "ckpt_name": (folder_paths.get_filename_list("checkpoints"), {
                    "tooltip": "The name of the checkpoint (model) to load."
                }),
            }
        }
    RETURN_TYPES = ("MODEL", "CLIP", "VAE")
    CATEGORY = "loaders"
```
- Loads and manages core ML models
- Often returns multiple model components
- Uses filesystem helpers for model discovery

### 2. Conditioning Nodes
Example: `ConditioningCombine`
```python
class ConditioningCombine:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "conditioning_1": ("CONDITIONING",),
            "conditioning_2": ("CONDITIONING",)
        }}
    RETURN_TYPES = ("CONDITIONING",)
    
    def combine(self, conditioning_1, conditioning_2):
        return (conditioning_1 + conditioning_2,)
```
- Manipulate CLIP embeddings and other conditioning data
- Often use PyTorch operations
- Return tuple matching RETURN_TYPES

### 3. VAE Processing Nodes
Example: `VAEEncode`
```python
class VAEEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "pixels": ("IMAGE",),
            "vae": ("VAE",)
        }}
    RETURN_TYPES = ("LATENT",)
```
- Handle image/latent conversion
- Work with pixel and latent space
- Often support tiling for large images

### 4. Resource Management Nodes
Example: `LoadLatent`
```python
class LoadLatent:
    @classmethod
    def IS_CHANGED(s, latent):
        # File hash checking
        return m.digest().hex()

    @classmethod
    def VALIDATE_INPUTS(s, latent):
        if not folder_paths.exists_annotated_filepath(latent):
            return "Invalid latent file: {}".format(latent)
        return True
```
- Handle file I/O operations
- Implement caching via IS_CHANGED
- Validate inputs and paths

## Implementation Best Practices

1. Return Value Pattern
   - Always return tuples matching RETURN_TYPES
   - Use descriptive RETURN_NAMES when helpful
   - Consider OUTPUT_TOOLTIPS for complex outputs

2. Input Validation
   - Use type constraints via INPUT_TYPES
   - Implement VALIDATE_INPUTS for complex checks
   - Add tooltips for user guidance

3. Error Handling
   - Use logging.warning for non-fatal issues
   - Return clear error messages
   - Validate paths and resources early

4. Performance Considerations
   - Cache expensive operations
   - Use IS_CHANGED for file operations
   - Consider tiled operations for large data

## Next Steps
1. Examine node execution flow
2. Study node communication patterns
3. Explore advanced node features
