# Cursor Rules for Consultant Toolkit

This directory contains Cursor AI rules to help guide development of the Consultant Toolkit project.

## Rules Overview

### Always Applied Rules
These rules are automatically applied to every AI interaction:

- **`project-structure.mdc`** - Overview of project architecture, file structure, and design patterns
- **`coding-conventions.mdc`** - General coding style, naming conventions, and best practices
- **`error-handling.mdc`** - Exception handling, logging patterns, and retry logic

### File-Type Specific Rules (Glob-Based)
These rules apply automatically when editing matching files:

- **`streamlit-patterns.mdc`** (`pages/*.py`, `**/streamlit*.py`, `Hello.py`)
  - Streamlit UI patterns, page structure, widgets, and user interaction

- **`python-standards.mdc`** (`*.py`)
  - Python-specific standards, type hints, class design, DataFrame operations

### Manual/Contextual Rules (Description-Based)
These rules can be manually applied when working on specific features:

- **`external-integrations.mdc`**
  - Description: "Working with external API integrations (OpenAI, Groq, SerpAPI, OxyLabs, YouTube)"
  - Patterns for API manager classes, error handling, rate limiting

- **`dataframe-processing.mdc`**
  - Description: "DataFrame processing, batch operations, and data transformation patterns"
  - DataFrameProcessor usage, batch handling, data transformations

- **`deployment-docker.mdc`**
  - Description: "Docker deployment and Railway configuration"
  - Docker setup, environment variables, logging, troubleshooting

## Quick Reference

### Key Files Referenced
- [Hello.py](mdc:Hello.py) - Main entry point
- [tool_configs.yaml](mdc:tool_configs.yaml) - Service configuration
- [src/setup.py](mdc:src/setup.py) - Configuration loading and credentials
- [src/external_tools.py](mdc:src/external_tools.py) - API integrations
- [src/streamlit_interface.py](mdc:src/streamlit_interface.py) - UI components
- [src/file_manager.py](mdc:src/file_manager.py) - Data persistence
- [src/batch_handler.py](mdc:src/batch_handler.py) - Batch processing

### Common Patterns

**Starting a New Page:**
```python
import logging
logger = logging.getLogger(__name__)
from src.streamlit_setup import page_setup, page_footer
import streamlit as st

page_config = {'page_title': "My Page", 'page_icon': "🔧"}
page_setup(page_config)

if st.session_state["authentication_status"]:
    session_logger = st.session_state["session_logger"]
    credential_manager = st.session_state['credential_manager']
    # Page content here

page_footer()
```

**Adding External API Integration:**
```python
class NewServiceManager:
    def __init__(self, session_logger: SessionLogger, credential_manager: CredentialManager):
        self.session_logger = session_logger
        self.credential_manager = credential_manager
        api_key = self.credential_manager.get_api_key('service_name')
        if not api_key:
            raise StopProcessingError("API key required")
        self.client = ServiceClient(api_key=api_key)
```

**Batch Processing DataFrames:**
```python
batches_constructor = DfBatchesConstructor(df_processor, session_logger)
for progress in batches_constructor.df_batches_handler(
    func=processing_function,
    response_column='result',
    query_column='input',
    batch_size=10
):
    if progress["df"] is None:
        update_progress(progress["processed_count"])
    else:
        final_df = progress["df"]
```

**Error Handling:**
```python
try:
    result = _exponential_backoff(
        func=lambda: api_call(),
        exc_types=(RetryableError,)
    )
except StopProcessingError as e:
    st.error(f"Critical error: {e}")
except SkippableError as e:
    logger.warning(f"Skipped: {e}")
```

## Customizing Rules

### To Add a New Rule:
1. Create a `.mdc` file in this directory
2. Add frontmatter with metadata:
   ```yaml
   ---
   alwaysApply: true  # or use globs/description
   ---
   ```
3. Write rule content in Markdown
4. Reference files using `[filename](mdc:path/to/filename)`

### Rule Types:
- **Always Apply**: Sets `alwaysApply: true`
- **Glob-Based**: Sets `globs: *.py,*.tsx`
- **Manual**: Sets `description: "When to use this rule"`

## Contributing to Rules

When you add new patterns or conventions to the codebase:
1. Document them in the appropriate rule file
2. Include code examples from actual project files
3. Use the `[filename](mdc:path)` syntax to reference files
4. Keep examples concise but complete

## Rule Maintenance

These rules should be updated when:
- New design patterns are established
- Dependencies are added or changed
- Project structure is reorganized
- New common errors or edge cases are discovered

Last updated: October 2025

