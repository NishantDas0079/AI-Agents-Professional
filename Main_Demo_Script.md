```python
#!/usr/bin/env python3
"""
Main demo script for Claude AI Agents
Run this to see all agents in action
"""
import sys
import os
sys.path.append(os.path.join(os.path.dirname(__file__), '..'))

from agents.research_agent import ResearchAgent
from agents.code_analyzer_agent import CodeAnalyzerAgent
from agents.data_visualizer_agent import DataVisualizerAgent
from agents.financial_agent import FinancialAnalystAgent
from agents.audio_agent import AudioAnalysisAgent
from agents.content_writer_agent import ContentWriterAgent
from agents.coordinator import MultiAgentCoordinator

def main():
    print("=" * 70)
    print("🤖 CLAUDE AI AGENTS - COMPREHENSIVE DEMO")
    print("=" * 70)
    print("Demonstrating 6 specialized AI agents with multi-agent coordination\n")
    
    try:
        # Initialize all agents
        print("1. 🚀 INITIALIZING AGENTS...")
        print("-" * 50)
        
        agents = {}
        
        # Create each agent
        agents['Research'] = (ResearchAgent("ResearchPro"), 
                             ["web_search", "analysis", "reporting"])
        agents['CodeAnalyzer'] = (CodeAnalyzerAgent(), 
                                 ["code_review", "security", "performance"])
        agents['DataVisualizer'] = (DataVisualizerAgent("VizMaster"), 
                                   ["charts", "graphs", "dashboards"])
        agents['Financial'] = (FinancialAnalystAgent("FinanceExpert"), 
                              ["stock_analysis", "portfolio", "markets"])
        agents['Audio'] = (AudioAnalysisAgent("SoundAnalyst"), 
                          ["audio_analysis", "waveforms", "spectrograms"])
        agents['ContentWriter'] = (ContentWriterAgent("WriterPro"), 
                                  ["articles", "summaries", "content"])
        
        print(f"\n✅ All {len(agents)} agents initialized successfully!")
        
        # Initialize coordinator
        print("\n2. 🤝 INITIALIZING MULTI-AGENT COORDINATOR...")
        coordinator = MultiAgentCoordinator("MasterCoordinator")
        coordinator.register_all_agents(agents)
        
        # Show initial status
        coordinator.show_agent_status()
        
        # Demo 1: Individual Agent Demos
        print("\n3. 🎯 INDIVIDUAL AGENT DEMONSTRATIONS...")
        print("=" * 50)
        
        # Research Agent Demo
        print("\n🔍 RESEARCH AGENT DEMO")
        research_agent = agents['Research'][0]
        search_results = research_agent.search("AI agents in healthcare", max_results=2)
        print(f"   Found {len(search_results)} research results")
        
        # Code Analyzer Demo
        print("\n👨‍💻 CODE ANALYZER DEMO")
        code_agent = agents['CodeAnalyzer'][0]
        sample_code = '''
def calculate(items):
    total = 0
    for item in items:
        total += item
    return total

password = "secret123"
'''
        analysis = code_agent.analyze(sample_code, "demo.py")
        print(f"   Code score: {analysis.get('score', 0)}/100")
        if analysis.get('issues', {}).get('security'):
            print(f"   Security issues: {len(analysis['issues']['security'])}")
        
        # Data Visualizer Demo
        print("\n🎨 DATA VISUALIZER DEMO")
        viz_agent = agents['DataVisualizer'][0]
        sales_data = {'Q1': 100, 'Q2': 150, 'Q3': 125, 'Q4': 200}
        print("   Creating sales chart...")
        viz_agent.create_chart(sales_data, chart_type="bar", title="Quarterly Sales")
        
        # Content Writer Demo
        print("\n✍️ CONTENT WRITER DEMO")
        writer_agent = agents['ContentWriter'][0]
        article = writer_agent.generate_article("Machine Learning", style="informative")
        print(f"   Generated article: {article['title']}")
        print(f"   Word count: {article['word_count']}")
        
        # Demo 2: Multi-Agent Coordination
        print("\n4. 🤝 MULTI-AGENT COORDINATION DEMO...")
        print("=" * 50)
        
        # Simple multi-agent task
        print("\n📋 Task: 'Analyze Python code and create visualization'")
        result = coordinator.execute_complex_task("Analyze Python code and create visualization")
        
        if result.get('combined_success', False):
            print(f"   ✅ Multi-agent task completed successfully!")
            print(f"   Sub-tasks: {result['total_sub_tasks']}")
            print(f"   Agents involved: {', '.join(result['agents_involved'])}")
        else:
            print("   ⚠️ Some sub-tasks failed")
        
        # Demo 3: Team Project
        print("\n5. 🏗️ TEAM PROJECT DEMO...")
        print("=" * 50)
        
        project_tasks = [
            {'description': 'Research latest AI developments', 'agent_type': 'research'},
            {'description': 'Analyze sample AI implementation code', 'agent_type': 'code'},
            {'description': 'Create report on AI trends', 'agent_type': 'content'},
            {'description': 'Visualize AI adoption data', 'agent_type': 'visual'}
        ]
        
        project_result = coordinator.team_project("AI Trends Analysis", project_tasks)
        
        print(f"\n📊 Project Summary:")
        print(f"   Project: {project_result['project_name']}")
        print(f"   Tasks completed: {len(project_result['tasks'])}")
        print(f"   Agents used: {len(project_result['agents_used'])}")
        print(f"   Total time: {project_result['total_time']:.2f}s")
        
        # Final Performance Report
        print("\n6. 📈 FINAL PERFORMANCE REPORT...")
        print("=" * 50)
        
        report = coordinator.get_performance_report()
        
        print("\n📋 AGENT PERFORMANCE SUMMARY:")
        for agent_name, stats in report['agents'].items():
            print(f"\n   {agent_name}:")
            print(f"      Tasks: {stats['tasks_completed']}")
            print(f"      Avg Time: {stats['avg_time_per_task']:.2f}s")
            print(f"      Efficiency: {stats['efficiency_score']:.1f}/100")
        
        print(f"\n📊 SYSTEM SUMMARY:")
        print(f"   Total tasks executed: {report['total_tasks']}")
        print(f"   Overall success rate: {report['summary']['success_rate']:.1f}%")
        print(f"   Most active agent: {report['summary']['most_active_agent']}")
        
        # Export data
        print("\n7. 💾 EXPORTING DATA...")
        history_file = coordinator.export_task_history("demo_task_history.json")
        print(f"   Task history saved to: {history_file}")
        
        # Create performance visualization
        print("\n8. 📊 CREATING PERFORMANCE VISUALIZATION...")
        fig = coordinator.visualize_performance()
        if fig:
            print("   Performance chart created!")
        
        print("\n" + "=" * 70)
        print("✅ DEMO COMPLETED SUCCESSFULLY!")
        print("=" * 70)
        
        print("\n🎯 What was demonstrated:")
        print("   • 6 Specialized AI Agents")
        print("   • Individual agent capabilities")
        print("   • Multi-agent coordination")
        print("   • Team project execution")
        print("   • Performance tracking and visualization")
        print("   • Data export capabilities")
        
        return 0
        
    except Exception as e:
        print(f"\n❌ Error during demo: {e}")
        import traceback
        traceback.print_exc()
        return 1

if __name__ == "__main__":
    sys.exit(main())
```
