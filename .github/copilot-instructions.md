# AI Coding Agent Instructions for demoDeepEval

## Project Overview
**demoDeepEval** is a Python evaluation framework using [DeepEval](https://github.com/confident-ai/deepeval) to measure LLM output quality with metrics like AnswerRelevancyMetric. This is a demonstration/learning project for LLM evaluation testing.

- **Architecture**: Single-script LLM evaluation setup
- **Key Dependency**: `deepeval>=3.7.3` (LLM evaluation framework)
- **Python Version**: 3.13+
- **Package Manager**: `uv` (not pip)

## Project Structure
```
.
├── main.py              # Entry point (currently minimal)
├── src/                 # Placeholder for reusable evaluation components
├── tests/
│   └── test_basic.py    # Core test using DeepEval metrics
├── pyproject.toml       # Project config with uv dependencies
└── .deepeval/           # DeepEval cache/config directory
```

## Critical Workflow Commands

### Running Tests
```bash
# Use uv run to execute tests with DeepEval CLI
uv run deepeval test run tests/test_basic.py

# Run tests with pytest directly (if needed)
uv run pytest tests/test_basic.py
```

**Important**: DeepEval wraps pytest and requires valid pytest configuration. Tests currently fail due to missing pytest plugin configuration.

### Development Setup
```bash
# Install dependencies via uv
uv sync

# Create virtual environment
uv venv
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows
```

## Known Issues & Patterns

### Pytest Plugin Configuration Issue
**Current Error**: `ModuleNotFoundError: No module named 'plugins'`
- **Root Cause**: DeepEval's `deepeval test run` command passes pytest a reference to a non-existent 'plugins' module
- **Impact**: Direct `deepeval test run` commands fail; plain pytest runs work
- **Workaround**: Use `uv run pytest` instead of `uv run deepeval test run` for immediate testing
- **Solution**: Add `pytest.ini` or `[tool.pytest.ini_options]` in `pyproject.toml` to explicitly configure plugins if needed

### DeepEval Test Structure
Tests in `tests/test_basic.py` follow DeepEval's pattern:
1. Create `LLMTestCase` with `input` and `actual_output`
2. Instantiate metric (e.g., `AnswerRelevancyMetric`) with threshold
3. Call `metric.measure(test_case)` to evaluate
4. Check `metric.score()` and `metric.success`

**Convention**: Assertions use both score comparison AND boolean `.success` check for robust test validation.

## AI Agent Best Practices

### When Modifying Tests
- Keep test structure aligned with DeepEval's `LLMTestCase` → metric → assertion pattern
- Always verify metrics have `.success` property before using it
- Test inputs should be meaningful for the metric being evaluated

### When Adding Dependencies
- Update `pyproject.toml` under `[project] dependencies`
- Run `uv sync` to update lock file
- Avoid direct `pip install` commands

### When Working with External LLMs
- Google Generative AI integration exists (`google-generativeai>=0.8.5` in dependencies)
- DeepEval may require API keys for certain metrics (e.g., using Claude, GPT for evaluations)
- Set environment variables as needed; don't hardcode credentials

## File Patterns to Know
- **Test files**: `tests/test_*.py` follow pytest discovery convention
- **Metrics**: DeepEval provides built-in metrics in `deepeval.metrics` module
- **`.deepeval/` directory**: Created automatically; cache for DeepEval runs—safe to ignore/gitignore

## Questions to Ask Before Implementation
- Should this extend evaluation metrics or add new test cases?
- Is the output intended for use with DeepEval's CI/CD integration or just local testing?
- Will this need API keys (for LLM-based metrics)? If so, document environment setup.
