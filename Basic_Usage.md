# Import and Use Individual Agents

```python
from agents.research_agent import ResearchAgent
from agents.code_analyzer_agent import CodeAnalyzerAgent

# Initialize agents
research_agent = ResearchAgent()
code_agent = CodeAnalyzerAgent()

# Use research agent
results = research_agent.search("AI multi-agent systems", max_results=3)
print(f"Found {len(results)} results")

# Use code analyzer
sample_code = '''
def calculate(items):
    total = 0
    for item in items:
        total += item
    return total
'''

analysis = code_agent.analyze(sample_code)
print(f"Code Score: {analysis['score']}/100")
```

# Use Multi Agent Coordinator
```python
from agents.coordinator import MultiAgentCoordinator
from agents.research_agent import ResearchAgent
from agents.data_visualizer_agent import DataVisualizerAgent

# Create coordinator
coordinator = MultiAgentCoordinator()

# Register agents
coordinator.register_agent("Research", ResearchAgent(), ["search", "analysis"])
coordinator.register_agent("Visualizer", DataVisualizerAgent(), ["charts", "graphs"])

# Execute task
result = coordinator.execute_task("Research AI trends and create visualization")
print(f"Task completed by: {result['agent']}")
```

# Run Demo
```
# Run comprehensive demo
python scripts/run_demo.py

# Run specific demo
python examples/basic_usage.ipynb
```

# 📊 Available Agents

Research Agent: Information gathering and analysis

Code Analyzer: Code review and security audit

Data Visualizer: Data visualization and charts

Financial Agent: Market analysis and portfolio tracking

Audio Agent: Audio analysis and visualization

Content Writer: Content generation and summarization

# 🐛 Troubleshooting
# Common Issues
ImportError: No module named 'agents'
```
# Make sure you're in the project root directory
cd Claude-AI-Agents-Professional

# Add current directory to Python path
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
```

Missing dependencies
```
# Reinstall requirements
pip install -r requirements.txt --upgrade
```

Plotly charts not showing
```
import plotly.io as pio
pio.renderers.default = "browser"  # Or "notebook" for Jupyter
```
