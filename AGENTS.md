# AGENTS.md - AI-ML-Notebooks Repository Guide

## Project Overview

This repository contains experimental Jupyter notebooks for converting legal statutes into LegalRuleML XML format using LLMs. It explores various prompting strategies (few-shot, in-context learning), model deployment options (OpenAI, Azure ML, local Llama2), and XML validation/similarity techniques.

**Project Type**: Research/experimental ML notebooks  
**Primary Domain**: Legal NLP / LegalRuleML generation  
**License**: GPL v3 (see LICENSE file)

## Repository Structure

```
AI-ML-Notebooks/
├── *.ipynb              # Jupyter notebooks (see descriptions below)
├── README.md            # Minimal project title only
├── LICENSE              # GPL v3 license
└── AGENTS.md           # This file

Notebook Inventory:
- ConceptualEncoding_Pydantic.ipynb    # Pydantic-based rule parsing for LegalRuleML
- LangChain_FewShot.ipynb              # Few-shot prompting with LangChain for statute conversion
- Llama2_AzureMl_CompletionsAPIAndChatAPI.ipynb  # Comparing Azure ML endpoints (Chat vs Completions)
- Llama_Documentation_Prompting.ipynb  # Prompting using LegalRuleML spec examples
- Local-portingmodelpy.ipynb           # Porting Azure ML workflow to local GPU inference
- LocalLlama-LangChain.ipynb           # Local Llama2 7B GPTQ testing with LangChain
- Metamodel-RAG.ipynb                  # RAG approach on LegalRuleML metamodel
- OpenAI_LegalRuleML.ipynb             # OpenAI API experiments with in-context learning
- Similarity-XML-SimpleRatio.ipynb     # Fuzzy string matching for XML similarity
- bnss_in_akomantoso.ipynb             # Working with Akoma Ntoso format
```

## Common Libraries & Dependencies

Notebooks typically use these Python libraries:

```python
# Core ML/AI
langchain / langchain-community / langchain-openai
openai
transformers
torch

# Data processing
pandas
numpy

# XML/HTML processing
xml.etree.ElementTree  # standard library
beautifulsoup4

# Similarity/validation
thefuzz                # Fuzzy string matching

# Utilities
python-dotenv          # Environment variable management
requests
```

**Key External Resources Referenced:**
- LegalRuleML Core Specification: http://docs.oasis-open.org/legalruleml/legalruleml-core-spec/v1.0/
- LegalRuleML Metamodel: http://docs.oasis-open.org/legalruleml/ns/v1.0/metamodel

## Running Notebooks

### Environment Setup

Most notebooks begin with pip installs for required packages:

```bash
# Common pattern found in notebooks
pip install langchain langchain-openai pandas beautifulsoup4 requests python-dotenv

# For local Llama2 inference (GPU required)
pip install transformers torch auto-gptq accelerate
```

### Notebook Patterns

1. **API Key Management**: Notebooks reference `secret_key.py` for OpenAI API keys (not committed)
2. **Azure ML Credentials**: Notebooks use endpoint URLs and API keys stored as empty strings (fill before running)
3. **Local Model Paths**: Local inference notebooks use HuggingFace model IDs like `TheBloke/Llama-2-7B-Chat-GPTQ`

## Key Architectural Patterns

### 1. Few-Shot Prompting for LegalRuleML

Standard pattern used across notebooks:

```python
examples = [
    {
        "question": "Statute: <legal text>",
        "answer": """<LegalRuleML xmlns="http://www.oasis-open.org/committees/legalruleml">
  <lrml:PrescriptiveStatement id="...">
    <!-- LegalRuleML structure -->
  </lrml:PrescriptiveStatement>
</LegalRuleML>"""
    }
]

prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="Question: {input}",
    input_variables=["input"],
)
```

### 2. LegalRuleML Structure Conventions

Generated XML follows these patterns:
- **Prescriptive Statements**: For obligations, permissions, prohibitions
- **Constitutive Statements**: For definitions and rule creation
- **Deontic Operators**: `<lrml:Obligation>`, `<lrml:Permission>`, `<lrml:Prohibition>`
- **Rule Structure**: `<lrml:if>` (condition) → `<lrml:then>` (consequence)
- **Namespace**: `xmlns:lrml="http://www.oasis-open.org/committees/legalruleml"`

### 3. Model Deployment Approaches

| Approach | Notebook | Notes |
|----------|----------|-------|
| OpenAI API | `OpenAI_LegalRuleML.ipynb` | Best results with GPT-4, requires API key |
| Azure ML Endpoints | `Llama2_AzureMl_*.ipynb` | Chat endpoint works better than Completions |
| Local (GPU) | `LocalLlama-LangChain.ipynb` | Uses GPTQ quantization for 7B models |

## Important Gotchas

### API Credentials
- **OpenAI**: Notebooks reference `from secret_key import openai_key` - this module is not in repo
- **Azure ML**: Endpoint URLs and keys are empty strings - must be configured manually
- **Never commit keys**: No keys are in git history, maintain this practice

### Context Window Limitations
- Local Llama2 7B has limited context (~1024-2048 tokens)
- Long statutes must be processed in sections/chunks
- XML generation for full statutes often truncates with local models

### XML Generation Consistency
- LLMs generate syntactically valid but semantically inconsistent XML
- Element naming varies between runs (e.g., `Intimation` vs `IntimateAuthority`)
- Manual review needed for element alignment with LegalRuleML spec

### Similarity Thresholds
- `Similarity-XML-SimpleRatio.ipynb` uses fuzzy matching
- Threshold of 0.5 (50%) considered meaningful for element matching
- False positives possible (e.g., "TimeUnit" matches "Timing" at 57%)

### LangChain Deprecation Warnings
- Several notebooks use older LangChain APIs (0.1.x era)
- `LLMChain`, `LLMMathChain` patterns may need updates for newer LangChain versions
- `HuggingFacePipeline` shows deprecation warnings in newer versions

## Testing Approach

No formal test suite exists. Validation is manual:

1. **Visual inspection** of generated XML
2. **Schema validation** (referenced but not automated in notebooks)
3. **Similarity comparison** between sequential generations
4. **Cross-reference** with LegalRuleML specification examples

## Naming Conventions

- **Files**: Descriptive with underscores, mentions approach/model
- **Notebook cells**: Markdown headers document approach sections
- **XML IDs**: CamelCase (e.g., `PunishmentForPiracy`, `AccidentReportingRule`)
- **Variables**: snake_case, descriptive

## Future Enhancement Areas

Based on notebook comments and TODOs:

1. **Grammar-based generation**: Llama's Grammar functionality mentioned for stricter XML validation
2. **ExampleSelector**: LangChain's dynamic example selection for few-shot prompting
3. **XML Tree comparison**: Beyond string similarity, structural comparison needed
4. **Systematic truncation strategy**: For processing full statutes with limited context windows

## Workflow Summary

For new LegalRuleML conversion work:

1. Prepare statute text (clean, structured sections)
2. Choose model based on constraints:
   - Best quality: OpenAI GPT-4
   - Cost-effective: Local Llama2 (requires GPU)
   - Enterprise: Azure ML Llama2 endpoints
3. Apply few-shot prompting with LegalRuleML spec examples
4. Post-process XML for consistency (manual review)
5. Use similarity metrics if processing statute in chunks
