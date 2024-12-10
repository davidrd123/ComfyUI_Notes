# ComfyUI Core Architecture Overview

## Core Architecture Overview

### Node Base Class (ComfyNodeABC)
The abstract base class that defines the contract for all ComfyUI nodes.

1. Essential Attributes
   ```python
   class ComfyNodeABC(ABC):
       DESCRIPTION: str    # Node tooltip description
       CATEGORY: str      # Menu categorization
       EXPERIMENTAL: bool # Flag for experimental features
       DEPRECATED: bool   # Flag for deprecated nodes
   ```

2. Input/Output Configuration
   ```python
   class ComfyNodeABC(ABC):
       @classmethod
       @abstractmethod
       def INPUT_TYPES(s) -> InputTypeDict:
           return {"required": {}}
       
       RETURN_TYPES: tuple[IO]     # Output types
       RETURN_NAMES: tuple[str]    # Output slot names
       OUTPUT_TOOLTIPS: tuple[str] # Output descriptions
   ```

3. Execution Control
   ```python
   class ComfyNodeABC(ABC):
       OUTPUT_NODE: bool           # Marks terminal nodes
       FUNCTION: str              # Execution entry point
       INPUT_IS_LIST: bool        # List input handling
       OUTPUT_IS_LIST: tuple[bool] # List output handling
   ```

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

1. Node Definition
   - Inherits from `ComfyNodeABC`
   - Must implement `INPUT_TYPES`
   - Configures execution through class attributes

2. Type Safety
   - Strong typing through `IO` enum
   - Type validation on node connections
   - Support for union types (e.g., `NUMBER = "FLOAT,INT"`)

3. UI Integration
   - Type information drives UI widget selection
   - Parameter constraints for input validation
   - Tooltips and documentation support

4. Advanced Features
   - Lazy evaluation support
   - Raw link access for node expansion
   - Hidden inputs for system integration
   - List processing capabilities
   - Terminal node marking
   - Experimental/deprecated flagging
   - Custom execution function specification