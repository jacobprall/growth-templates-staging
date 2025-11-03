# Template Specifications

## What is a Template?

- Interactive, focused introductions to Snowflake features/use cases
- Executable worksheets, notebooks, or Streamlit apps
- Pre-configured with sample data to minimize setup
- Designed for completion in under 5 minutes
- Tactical examples that bridge to real-world application

Templates must demonstrate SQL and/or Python-first features, capabilities, or use cases entirely within the coding environment. Each template should focus on a single, well-defined learning objective that remains stable. This will enable marketers to leverage templates in developer guides, marketing campaigns, and educational materials without fear of breaking changes.

### Learning Objectives

Learning objectives will generally fall into one of three categories:

- **Product-focus**: Understanding the capabilities of a Snowflake product and how they fit together
- **Feature-focus**: Mastering a specific feature or functionality of a Snowflake product
- **Use Case-focus**: Solving real-world data engineering problems or end-to-end workflows

## Constraints & Capabilities

- **Single file format**: `.ipynb` (preferred) or `.sql`
- **Schema-level operations only**: No account-level changes
- **Common dataset**: Leverage "Tasty Bytes" (loaded from S3)
- **No external dependencies**: Must run without external services
- **Built-in API access**: Use `_snowflake` library for REST API calls
- **Output token limit**: 8192 tokens (build incrementally if needed)

### Cleanup Requirements

- **Start clean**: Remove existing objects from previous runs
- **End clean**: Clean up all created resources
- **Non-destructive**: All changes must be reversible

## Content Structure

### Title (36 characters max)

- **Introductory**: "Intro to X"
- **Use case**: Start with action verb ("Analyze Customer Churn...")  
- **Reference**: "Quick Reference: [Topic]"

### Overview (1-3 sentences)

- Summarize functionality and value
- Include business context when relevant
- Set clear expectations for outcome

### Structured Steps

- **Progressive complexity**: Build from simple to advanced
- **Markdown explanations**: Precede each code block with context
- **Executable progression**: Each step builds on the previous

### Key Takeaways

- Reinforce main learning outcomes
- Highlight practical insights gained
- Connect to broader use cases

### Additional Resources

- Documentation links
- Related templates

## Content Standards

### Scope & Focus

- **Single feature/use case**: Avoid combining unrelated concepts
- **Hands-on over theory**: Minimize explanation, maximize action
- **Universal accessibility**: Clear for all experience levels
- **Actionable outcomes**: Guide to concrete insights or completed tasks

### Voice & Tone

- **Active voice**: "Run this query" not "This query can be used"
- **Conversational professionalism**: Natural but authoritative
- **Jargon-free**: Spell out acronyms, explain technical terms
- **Natural contractions**: Write as you'd speak

### Best Practices

- Provide application context to show how features work in context
- Maintain readability through inline comments and scannable formatting 
- Keep paragraphs short (3-4 lines maximum) and use clear headings
- Front-load the most important information 
- Be opinionated in our recommendations and honest when discussing tradeoffs

## Code Conventions

### SQL Standards

- **UPPERCASE keywords**: `SELECT`, `FROM`, `WHERE`, etc.
- **Executable without modification**: Copy-paste ready
- **Descriptive comments**: Explain complex logic inline
- **Proper formatting**: Consistent indentation and spacing

### Python Standards

- **Clear imports**: Explicitly import required packages
- **Snowflake session**: Use `get_active_session()` for connections
- **Streamlit integration**: Include when appropriate for interactivity
- **API integration**: Leverage `_snowflake` library for REST calls

## Environment Setup
```sql
USE ROLE SNOWFLAKE_LEARNING_ROLE;
USE WAREHOUSE SNOWFLAKE_LEARNING_WH;
USE DATABASE SNOWFLAKE_LEARNING_DB;
SET schema_name = CONCAT(current_user(), '_<TEMPLATE_ID>');
USE SCHEMA IDENTIFIER($schema_name);
```

## API Usage Pattern

```python
import _snowflake
import json

resp = _snowflake.send_snow_api_request(
    "POST",
    f"/api/v2/cortex/analyst/message",
    {},  # headers
    {},  # params  
    request_body,
    None,  # request_guid
    30000  # timeout
)

if resp["status"] < 400:
    return json.loads(resp["content"]), None
```
