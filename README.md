# AI-ML-Notebooks

Experimental Jupyter notebooks for converting legal statutes into [LegalRuleML](http://docs.oasis-open.org/legalruleml/legalruleml-core-spec/v1.0/) XML format using Large Language Models.

## Overview

This repository contains research notebooks exploring various approaches to automate the conversion of legal text into machine-readable LegalRuleML format. The work compares different LLM providers (OpenAI GPT-4, Azure ML Llama2, local inference) and prompting strategies (few-shot, in-context learning, RAG).

## Repository Contents

| Notebook | Description |
|----------|-------------|
| `OpenAI_LegalRuleML.ipynb` | In-context learning experiments with OpenAI GPT-4 |
| `LangChain_FewShot.ipynb` | Few-shot prompting pipeline using LangChain |
| `LocalLlama-LangChain.ipynb` | Local Llama2 7B GPTQ inference testing |
| `Llama2_AzureMl_CompletionsAPIAndChatAPI.ipynb` | Azure ML endpoint comparisons |
| `Metamodel-RAG.ipynb` | RAG approach using LegalRuleML metamodel |
| `Similarity-XML-SimpleRatio.ipynb` | Fuzzy matching for XML consistency |
| `ConceptualEncoding_Pydantic.ipynb` | Pydantic-based rule parsing |
| `bnss_in_akomantoso.ipynb` | Akoma Ntoso format experiments |

## Quick Start

```bash
# Install dependencies (varies by notebook)
pip install langchain langchain-openai transformers torch pandas

# For local GPU inference
pip install auto-gptq accelerate

# Run notebooks
jupyter notebook
```

## Key Dependencies

- **LangChain** (0.1.x) - Prompt chaining and LLM orchestration
- **OpenAI** - GPT-4 API access
- **Transformers** - HuggingFace model loading
- **Pandas** - Data processing
- **TheFuzz** - String similarity for XML validation

## Documentation

See [AGENTS.md](./AGENTS.md) for detailed guidance on working with this codebase, including:
- Architecture patterns and conventions
- API credential management
- Known limitations and gotchas
- Workflow recommendations

## License

GPL v3 - See [LICENSE](./LICENSE)

## Project Context

Developed as part of research into automated legal document markup, specifically targeting the LegalRuleML standard for representing prescriptive and constitutive legal rules in XML.
