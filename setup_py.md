```python
from setuptools import setup, find_packages

with open("README.md", "r", encoding="utf-8") as fh:
    long_description = fh.read()

with open("requirements.txt", "r", encoding="utf-8") as fh:
    requirements = [line.strip() for line in fh if line.strip() and not line.startswith("#")]

setup(
    name="claude-ai-agents",
    version="1.0.0",
    author="Nishant Das",
    author_email="nishant@example.com",
    description="A comprehensive suite of AI agents for research, code review, visualization, finance, audio analysis, and content creation",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/NishantDas0079/Claude-AI-Agents-Professional",
    packages=find_packages(),
    classifiers=[
        "Development Status :: 5 - Production/Stable",
        "Intended Audience :: Developers",
        "Intended Audience :: Education",
        "Topic :: Scientific/Engineering :: Artificial Intelligence",
        "License :: OSI Approved :: MIT License",
        "Programming Language :: Python :: 3",
        "Programming Language :: Python :: 3.8",
        "Programming Language :: Python :: 3.9",
        "Programming Language :: Python :: 3.10",
        "Programming Language :: Python :: 3.11",
    ],
    python_requires=">=3.8",
    install_requires=requirements,
    extras_require={
        "dev": [
            "pytest>=7.0",
            "pytest-cov>=4.0",
            "black>=23.0",
            "flake8>=6.0",
            "mypy>=1.5",
        ],
        "docs": [
            "sphinx>=7.0",
            "sphinx-rtd-theme>=1.3",
        ],
    },
    entry_points={
        "console_scripts": [
            "claude-agents=scripts.run_demo:main",
        ],
    },
    include_package_data=True,
    keywords="ai agents, machine learning, data science, visualization, automation",
    project_urls={
        "Bug Reports": "https://github.com/NishantDas0079/Claude-AI-Agents-Professional/issues",
        "Source": "https://github.com/NishantDas0079/Claude-AI-Agents-Professional",
        "Documentation": "https://github.com/NishantDas0079/Claude-AI-Agents-Professional/docs",
    },
)
```
