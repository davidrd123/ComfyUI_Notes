# ComfyUI Nodes Documentation

## Core Architecture Overview

### Type System
1. Base Class: `IO` (StrEnum)
   ```python
   class IO(StrEnum):
       # Basic Types
       STRING = "STRING"
       INT = "INT"
       FLOAT = "FLOAT"
       BOOLEAN = "BOOLEAN"
       
       # Model Types
       MODEL = "MODEL"
       CLIP = "CLIP"
       VAE = "VAE"
       CLIP_VISION = "CLIP_VISION"
       
       # Processing Types
       IMAGE = "IMAGE"
       MASK = "MASK"
       LATENT = "LATENT"
       CONDITIONING = "CONDITIONING"
       
       # Special Types
       ANY = "*"  # Universal matcher
       NUMBER = "FLOAT,INT"  # Numeric types
       PRIMITIVE = "STRING,FLOAT,INT,BOOLEAN"
   ```

2. Input Type Configuration
   ```python
   class InputTypeOptions(TypedDict):
       # Common Options
       default: bool | str | float | int | list | tuple
       tooltip: str
       
       # Widget Control
       defaultInput: bool  # Default to input slot
       forceInput: bool   # Force input slot
       
       # Evaluation
       lazy: bool         # Lazy evaluation
       rawLink: bool      # Get raw link info
       
       # Type-Specific Options
       # Numbers (FLOAT/INT)
       min: float
       max: float
       step: float
       round: float      # FLOAT only
       
       # Strings
       multiline: bool
       placeholder: str
       dynamicPrompts: bool
   ```

3. Node Type Interface
   ```python
   class InputTypeDict(TypedDict):
       required: dict[str, tuple[IO, InputTypeOptions]]
       optional: dict[str, tuple[IO, InputTypeOptions]]
       hidden: HiddenInputTypeDict  # System inputs
   ```

4. Hidden System Types
   - UNIQUE_ID: Node identifier
   - PROMPT: Complete workflow prompt
   - EXTRA_PNGINFO: Metadata for saved images
   - DYNPROMPT: Dynamic prompt handling

### Key Concepts
1. Type Safety
   - Strong typing through IO enum
   - Type validation on node connections
   - Support for union types (e.g., NUMBER = "FLOAT,INT")

2. UI Integration
   - Type information drives UI widget selection
   - Parameter constraints for input validation
   - Tooltips and documentation support

3. Advanced Features
   - Lazy evaluation support
   - Raw link access for node expansion
   - Hidden inputs for system integration

### Next Areas to Explore
1. Node Base Class (ComfyNodeABC)
2. Node Registration System
3. Execution Flow
