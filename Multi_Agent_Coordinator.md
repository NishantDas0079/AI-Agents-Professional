```python
"""
Multi-Agent Coordinator - Manages all agents and their interactions
"""
from typing import Dict, List, Any, Optional
from datetime import datetime
import json

class MultiAgentCoordinator:
    """Coordinates multiple specialized agents for complex tasks"""
    
    def __init__(self, name="AgentCoordinator"):
        self.name = name
        self.agents = {}
        self.task_history = []
        self.performance_metrics = {}
        print(f"🤝 {name} initialized! Ready to coordinate AI agents.")
    
    def register_agent(self, agent_name: str, agent_instance: Any, capabilities: List[str]):
        """Register an agent with the coordinator"""
        self.agents[agent_name] = {
            'instance': agent_instance,
            'capabilities': capabilities,
            'status': 'available',
            'tasks_completed': 0,
            'total_time': 0,
            'registered_at': datetime.now().isoformat()
        }
        print(f"✅ Registered: {agent_name} ({', '.join(capabilities[:3])})")
    
    def register_all_agents(self, agents_dict: Dict):
        """Register multiple agents at once"""
        for name, (instance, caps) in agents_dict.items():
            self.register_agent(name, instance, caps)
    
    def execute_task(self, task_description: str, agent_type: Optional[str] = None) -> Dict:
        """Execute a task using appropriate agent"""
        print(f"\n📋 Task: {task_description}")
        
        import time
        start_time = time.time()
        
        # Find suitable agents
        suitable_agents = self._find_suitable_agents(task_description, agent_type)
        
        if not suitable_agents:
            return {
                'success': False,
                'error': 'No suitable agent found',
                'task': task_description
            }
        
        # Select best agent (simple round-robin for demo)
        selected_agent_name, agent_info = suitable_agents[0]
        agent = agent_info['instance']
        
        print(f"🤖 Assigning to: {selected_agent_name}")
        
        # Update agent status
        self.agents[selected_agent_name]['status'] = 'busy'
        
        # Execute task based on agent type
        result = self._execute_agent_task(agent, task_description, selected_agent_name)
        
        # Update metrics
        execution_time = time.time() - start_time
        self.agents[selected_agent_name].update({
            'status': 'available',
            'tasks_completed': self.agents[selected_agent_name]['tasks_completed'] + 1,
            'total_time': self.agents[selected_agent_name]['total_time'] + execution_time
        })
        
        # Log task
        task_log = {
            'task': task_description,
            'agent': selected_agent_name,
            'timestamp': datetime.now().isoformat(),
            'execution_time': round(execution_time, 2),
            'success': 'error' not in result,
            'result_summary': str(result)[:200] + '...'
        }
        self.task_history.append(task_log)
        
        # Update performance metrics
        self._update_performance_metrics(selected_agent_name, execution_time, 'error' not in result)
        
        return {
            'success': 'error' not in result,
            'agent': selected_agent_name,
            'execution_time': round(execution_time, 2),
            'result': result,
            'task_log': task_log
        }
    
    def execute_complex_task(self, task_description: str) -> Dict:
        """Execute complex task requiring multiple agents"""
        print(f"\n🏗️ Complex Task: {task_description}")
        print("-" * 50)
        
        # Decompose complex task (simplified)
        sub_tasks = self._decompose_task(task_description)
        
        if not sub_tasks:
            return self.execute_task(task_description)
        
        results = []
        for i, sub_task in enumerate(sub_tasks, 1):
            print(f"\n📌 Sub-task {i}: {sub_task['description']}")
            result = self.execute_task(sub_task['description'], sub_task.get('agent_type'))
            results.append(result)
        
        # Combine results
        combined_result = self._combine_results(results)
        
        print("\n" + "=" * 50)
        print(f"✅ Complex Task Complete!")
        print(f"   Sub-tasks: {len(results)}")
        print(f"   Successful: {len([r for r in results if r['success']])}")
        
        return combined_result
    
    def team_project(self, project_name: str, tasks: List[Dict]) -> Dict:
        """Coordinate a complete project with multiple agents"""
        print(f"\n🏗️ Starting Project: {project_name}")
        print("=" * 60)
        
        project_results = {
            'project_name': project_name,
            'start_time': datetime.now().isoformat(),
            'tasks': [],
            'agents_used': set(),
            'total_time': 0
        }
        
        for i, task in enumerate(tasks, 1):
            print(f"\n📌 Task {i}: {task['description']}")
            
            result = self.execute_task(
                task['description'],
                task.get('agent_type')
            )
            
            project_results['tasks'].append(result)
            if 'agent' in result:
                project_results['agents_used'].add(result['agent'])
            if 'execution_time' in result:
                project_results['total_time'] += result['execution_time']
        
        project_results['end_time'] = datetime.now().isoformat()
        project_results['agents_used'] = list(project_results['agents_used'])
        
        print("\n" + "=" * 60)
        print(f"✅ Project '{project_name}' Complete!")
        print(f"   Total Tasks: {len(tasks)}")
        print(f"   Agents Used: {len(project_results['agents_used'])}")
        print(f"   Total Time: {project_results['total_time']:.2f}s")
        
        return project_results
    
    def show_agent_status(self):
        """Display current status of all agents"""
        print("\n👥 AGENT STATUS")
        print("=" * 50)
        
        for agent_name, agent_info in self.agents.items():
            status_icon = "🟢" if agent_info['status'] == 'available' else "🟡"
            avg_time = agent_info['total_time'] / agent_info['tasks_completed'] if agent_info['tasks_completed'] > 0 else 0
            
            print(f"{status_icon} {agent_name}")
            print(f"   Status: {agent_info['status']}")
            print(f"   Tasks: {agent_info['tasks_completed']} completed")
            print(f"   Avg Time: {avg_time:.2f}s")
            print(f"   Capabilities: {', '.join(agent_info['capabilities'][:3])}")
            if len(agent_info['capabilities']) > 3:
                print(f"             +{len(agent_info['capabilities']) - 3} more")
            print()
    
    def get_performance_report(self) -> Dict:
        """Generate performance report for all agents"""
        report = {
            'timestamp': datetime.now().isoformat(),
            'total_agents': len(self.agents),
            'total_tasks': len(self.task_history),
            'agents': {},
            'summary': {}
        }
        
        for agent_name, agent_info in self.agents.items():
            report['agents'][agent_name] = {
                'tasks_completed': agent_info['tasks_completed'],
                'total_time': agent_info['total_time'],
                'avg_time_per_task': agent_info['total_time'] / agent_info['tasks_completed'] if agent_info['tasks_completed'] > 0 else 0,
                'availability': agent_info['status'],
                'efficiency_score': self._calculate_efficiency_score(agent_info)
            }
        
        # Calculate summary statistics
        successful_tasks = len([t for t in self.task_history if t['success']])
        report['summary'] = {
            'success_rate': (successful_tasks / len(self.task_history) * 100) if self.task_history else 0,
            'avg_task_time': sum(t['execution_time'] for t in self.task_history) / len(self.task_history) if self.task_history else 0,
            'busy_agents': sum(1 for a in self.agents.values() if a['status'] == 'busy'),
            'most_active_agent': max(self.agents.items(), key=lambda x: x[1]['tasks_completed'])[0] if self.agents else None
        }
        
        return report
    
    def _find_suitable_agents(self, task_description: str, agent_type: Optional[str]) -> List:
        """Find agents suitable for a given task"""
        suitable = []
        
        for agent_name, agent_info in self.agents.items():
            # Check if agent type matches (if specified)
            if agent_type and agent_type not in agent_name.lower():
                continue
            
            # Check capabilities (simplified keyword matching)
            task_lower = task_description.lower()
            capabilities_lower = [c.lower() for c in agent_info['capabilities']]
            
            # Simple matching logic
            matches = sum(1 for cap in capabilities_lower if any(word in task_lower for word in cap.split()))
            if matches > 0 or not agent_type:
                suitable.append((agent_name, agent_info))
        
        # Sort by availability and then by tasks completed (load balancing)
        suitable.sort(key=lambda x: (
            0 if x[1]['status'] == 'available' else 1,
            x[1]['tasks_completed']
        ))
        
        return suitable
    
    def _execute_agent_task(self, agent, task_description: str, agent_name: str) -> Any:
        """Execute task based on agent type"""
        task_lower = task_description.lower()
        
        try:
            # Research Agent
            if 'research' in agent_name.lower():
                if hasattr(agent, 'search'):
                    return agent.search(task_description, max_results=2)
            
            # Code Analyzer
            elif 'code' in agent_name.lower():
                if hasattr(agent, 'analyze'):
                    # For code analysis tasks, use sample code
                    sample_code = '''
def example():
    return "Sample code for analysis"
'''
                    return agent.analyze(sample_code, "task_code.py")
            
            # Data Visualizer
            elif 'visual' in agent_name.lower() or 'viz' in agent_name.lower():
                if hasattr(agent, 'create_chart'):
                    sample_data = {'A': 10, 'B': 20, 'C': 15, 'D': 25}
                    return agent.create_chart(sample_data, title=task_description)
            
            # Financial Agent
            elif 'finance' in agent_name.lower() or 'stock' in agent_name.lower():
                if hasattr(agent, 'analyze_stock'):
                    return agent.analyze_stock("MOCK", 30)
            
            # Audio Agent
            elif 'audio' in agent_name.lower():
                if hasattr(agent, 'generate_waveform'):
                    return agent.generate_waveform(440, 1, task_description)
            
            # Content Writer
            elif 'content' in agent_name.lower() or 'writer' in agent_name.lower():
                if hasattr(agent, 'generate_article'):
                    return agent.generate_article(task_description)
            
            # Default fallback
            if hasattr(agent, '__call__'):
                return agent(task_description)
            else:
                return {'message': f'Task executed by {agent_name}', 'task': task_description}
                
        except Exception as e:
            return {'error': str(e), 'agent': agent_name, 'task': task_description}
    
    def _decompose_task(self, task_description: str) -> List[Dict]:
        """Decompose complex task into sub-tasks (simplified)"""
        task_lower = task_description.lower()
        sub_tasks = []
        
        # Research + Analysis pattern
        if ('research' in task_lower and 'analyze' in task_lower) or \
           ('analyze' in task_lower and 'report' in task_lower):
            sub_tasks = [
                {'description': f'Research: {task_description}', 'agent_type': 'research'},
                {'description': f'Analyze findings from research', 'agent_type': 'code'},
                {'description': f'Create report on analysis', 'agent_type': 'content'}
            ]
        
        # Data Analysis + Visualization pattern
        elif ('data' in task_lower and 'visual' in task_lower) or \
             ('analyze' in task_lower and 'chart' in task_lower):
            sub_tasks = [
                {'description': f'Analyze data: {task_description}', 'agent_type': 'code'},
                {'description': f'Create visualization of analysis', 'agent_type': 'visual'}
            ]
        
        return sub_tasks
    
    def _combine_results(self, results: List[Dict]) -> Dict:
        """Combine results from multiple agents"""
        successful = [r for r in results if r['success']]
        failed = [r for r in results if not r['success']]
        
        combined = {
            'combined_success': len(failed) == 0,
            'total_sub_tasks': len(results),
            'successful_sub_tasks': len(successful),
            'failed_sub_tasks': len(failed),
            'agents_involved': list(set(r.get('agent', 'unknown') for r in results)),
            'total_execution_time': sum(r.get('execution_time', 0) for r in results),
            'sub_task_results': results,
            'summary': self._generate_combined_summary(results)
        }
        
        return combined
    
    def _generate_combined_summary(self, results: List[Dict]) -> str:
        """Generate summary of combined results"""
        if not results:
            return "No results to summarize"
        
        agents = set(r.get('agent', 'unknown') for r in results)
        success_rate = (len([r for r in results if r['success']]) / len(results)) * 100
        
        summary = f"Combined {len(results)} tasks executed by {len(agents)} agents. "
        summary += f"Success rate: {success_rate:.1f}%. "
        
        if success_rate == 100:
            summary += "All tasks completed successfully."
        elif success_rate > 70:
            summary += "Most tasks completed successfully."
        else:
            summary += "Several tasks encountered issues."
        
        return summary
    
    def _update_performance_metrics(self, agent_name: str, execution_time: float, success: bool):
        """Update performance metrics for an agent"""
        if agent_name not in self.performance_metrics:
            self.performance_metrics[agent_name] = {
                'total_tasks': 0,
                'successful_tasks': 0,
                'total_time': 0,
                'avg_time': 0,
                'success_rate': 0
            }
        
        metrics = self.performance_metrics[agent_name]
        metrics['total_tasks'] += 1
        metrics['total_time'] += execution_time
        metrics['avg_time'] = metrics['total_time'] / metrics['total_tasks']
        
        if success:
            metrics['successful_tasks'] += 1
        
        metrics['success_rate'] = (metrics['successful_tasks'] / metrics['total_tasks']) * 100
    
    def _calculate_efficiency_score(self, agent_info: Dict) -> float:
        """Calculate efficiency score for an agent"""
        if agent_info['tasks_completed'] == 0:
            return 0
        
        # Score based on tasks completed and average time
        task_score = min(agent_info['tasks_completed'] * 10, 100)
        time_score = max(0, 100 - (agent_info['total_time'] / agent_info['tasks_completed'] * 10))
        
        # Combine scores
        efficiency = (task_score * 0.6 + time_score * 0.4)
        return min(100, max(0, efficiency))
    
    def export_task_history(self, filename: str = "task_history.json"):
        """Export task history to JSON file"""
        export_data = {
            'export_time': datetime.now().isoformat(),
            'total_tasks': len(self.task_history),
            'task_history': self.task_history,
            'agent_summary': {
                name: {
                    'tasks_completed': info['tasks_completed'],
                    'total_time': info['total_time']
                }
                for name, info in self.agents.items()
            }
        }
        
        with open(filename, 'w') as f:
            json.dump(export_data, f, indent=2, default=str)
        
        print(f"📤 Task history exported to {filename}")
        return filename
    
    def visualize_performance(self):
        """Create visualization of agent performance"""
        try:
            import plotly.graph_objects as go
            
            agent_names = list(self.agents.keys())
            tasks_completed = [agent['tasks_completed'] for agent in self.agents.values()]
            avg_times = []
            
            for agent_info in self.agents.values():
                if agent_info['tasks_completed'] > 0:
                    avg_times.append(agent_info['total_time'] / agent_info['tasks_completed'])
                else:
                    avg_times.append(0)
            
            # Create figure with secondary y-axis
            fig = go.Figure()
            
            # Tasks completed (bar chart)
            fig.add_trace(go.Bar(
                x=agent_names,
                y=tasks_completed,
                name='Tasks Completed',
                marker_color='lightblue',
                yaxis='y'
            ))
            
            # Average time (line chart)
            fig.add_trace(go.Scatter(
                x=agent_names,
                y=avg_times,
                name='Avg Time (s)',
                line=dict(color='red', width=2),
                yaxis='y2'
            ))
            
            # Update layout
            fig.update_layout(
                title='Agent Performance Metrics',
                xaxis_title='Agents',
                yaxis_title='Tasks Completed',
                yaxis2=dict(
                    title='Average Time (seconds)',
                    overlaying='y',
                    side='right'
                ),
                hovermode='x unified',
                template='plotly_white'
            )
            
            fig.show()
            return fig
            
        except ImportError:
            print("⚠️ Plotly not installed. Install with: pip install plotly")
            return None

# Quick test
if __name__ == "__main__":
    # Create mock agents for testing
    class MockAgent:
        def __init__(self, name):
            self.name = name
        
        def search(self, query, max_results=2):
            return [{'title': f'Result for {query}', 'summary': 'Mock result'}]
        
        def analyze(self, code, filename):
            return {'score': 85, 'issues': ['Mock issue']}
    
    print("Testing Multi-Agent Coordinator...")
    
    coordinator = MultiAgentCoordinator()
    
    # Register mock agents
    coordinator.register_agent(
        "ResearchAgent",
        MockAgent("Research"),
        ["web_search", "analysis", "reporting"]
    )
    
    coordinator.register_agent(
        "CodeAnalyzer",
        MockAgent("Code"),
        ["code_review", "security", "performance"]
    )
    
    # Test task execution
    result = coordinator.execute_task("Research AI trends", "research")
    print(f"✅ Task executed: {result['success']}")
    print(f"   Agent used: {result.get('agent', 'unknown')}")
    
    # Show status
    coordinator.show_agent_status()
    
    # Get performance report
    report = coordinator.get_performance_report()
    print(f"\n📊 Performance Report:")
    print(f"   Total agents: {report['total_agents']}")
    print(f"   Total tasks: {report['total_tasks']}")
    print(f"   Success rate: {report['summary']['success_rate']:.1f}%")
    
    print("\n🎉 Multi-Agent Coordinator is working!")
```
