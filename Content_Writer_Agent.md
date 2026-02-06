```python
"""
Content Writer Agent - AI-powered content generation and summarization
"""
import re
from typing import List, Dict, Optional
from datetime import datetime
from textblob import TextBlob
import nltk
from nltk.tokenize import sent_tokenize, word_tokenize
from nltk.corpus import stopwords

class ContentWriterAgent:
    """AI agent for content creation, summarization, and text analysis"""
    
    def __init__(self, name="ContentWriter"):
        self.name = name
        self.content_history = []
        
        # Download NLTK data if needed
        try:
            nltk.data.find('tokenizers/punkt')
            nltk.data.find('corpora/stopwords')
        except LookupError:
            nltk.download('punkt', quiet=True)
            nltk.download('stopwords', quiet=True)
        
        print(f"✍️ {name} ready! Specialized in content creation and analysis.")
    
    def generate_article(self, topic: str, length: str = "medium", style: str = "informative") -> Dict:
        """Generate an article on a given topic"""
        print(f"📝 Generating {length} {style} article about: {topic}")
        
        # Article templates based on style
        templates = {
            "informative": {
                "intro": "This article explores the key aspects of {topic}.",
                "body": "One important consideration is {aspect1}. Additionally, {aspect2} plays a crucial role.",
                "conclusion": "In summary, {topic} represents an important development in its field."
            },
            "persuasive": {
                "intro": "There's compelling evidence that {topic} is essential for modern applications.",
                "body": "The benefits are clear: {benefit1}. Furthermore, {benefit2} cannot be ignored.",
                "conclusion": "Therefore, adopting {topic} is not just advisable, but necessary."
            },
            "technical": {
                "intro": "This technical analysis examines {topic} from an engineering perspective.",
                "body": "The architecture involves {component1} and {component2}. Performance metrics indicate {metric}.",
                "conclusion": "Technical implementation of {topic} requires careful consideration of these factors."
            }
        }
        
        # Generate content based on template
        template = templates.get(style, templates["informative"])
        
        # Create placeholder aspects/benefits
        aspects = [
            f"understanding the fundamental principles",
            f"considering practical applications",
            f"evaluating current implementations",
            f"assessing future developments"
        ]
        
        article = {
            'title': f"Comprehensive Analysis of {topic.title()}",
            'topic': topic,
            'style': style,
            'length': length,
            'generated_at': datetime.now().isoformat(),
            'content': {
                'introduction': template['intro'].format(topic=topic),
                'body_paragraphs': [
                    template['body'].format(
                        aspect1=aspects[0],
                        aspect2=aspects[1],
                        benefit1="increased efficiency",
                        benefit2="enhanced scalability",
                        component1="modular design",
                        component2="distributed processing",
                        metric="significant performance improvements"
                    ),
                    f"Another critical aspect is {aspects[2]}. This involves detailed examination of underlying mechanisms.",
                    f"Practical considerations include {aspects[3]}, which affects real-world deployment."
                ],
                'conclusion': template['conclusion'].format(topic=topic),
                'key_points': [
                    f"{topic} offers multiple advantages for modern systems",
                    f"Implementation requires careful planning and testing",
                    f"Future developments will likely expand its applications"
                ]
            },
            'word_count': 0,
            'readability_score': 0
        }
        
        # Calculate word count
        full_text = self._combine_article(article['content'])
        article['word_count'] = len(full_text.split())
        
        # Calculate readability
        article['readability_score'] = self._calculate_readability(full_text)
        
        # Add to history
        self.content_history.append({
            'action': 'generate_article',
            'topic': topic,
            'timestamp': datetime.now().isoformat(),
            'word_count': article['word_count']
        })
        
        return article
    
    def summarize_text(self, text: str, summary_length: str = "brief") -> Dict:
        """Summarize long text content"""
        print(f"📋 Creating {summary_length} summary...")
        
        # Tokenize sentences
        sentences = sent_tokenize(text)
        
        if len(sentences) <= 3:
            return {
                'original_length': len(text.split()),
                'summary': text,
                'reduction': 0,
                'key_sentences': sentences
            }
        
        # Calculate sentence importance (simplified)
        sentence_scores = []
        stop_words = set(stopwords.words('english'))
        
        for i, sentence in enumerate(sentences):
            # Simple scoring based on position and length
            words = word_tokenize(sentence.lower())
            content_words = [w for w in words if w not in stop_words and w.isalnum()]
            
            score = len(content_words) * 1.5  # Weight for content words
            if i == 0 or i == len(sentences) - 1:
                score *= 1.3  # Boost first/last sentences
            if any(keyword in sentence.lower() for keyword in ['important', 'key', 'significant', 'major']):
                score *= 1.5
            
            sentence_scores.append((sentence, score, i))
        
        # Select top sentences based on summary length
        num_sentences = {
            'brief': max(2, len(sentences) // 5),
            'medium': max(3, len(sentences) // 3),
            'detailed': max(5, len(sentences) // 2)
        }.get(summary_length, 3)
        
        # Sort by score and get top sentences in original order
        top_sentences = sorted(sentence_scores, key=lambda x: x[1], reverse=True)[:num_sentences]
        top_sentences.sort(key=lambda x: x[2])  # Restore original order
        
        summary = ' '.join([s[0] for s in top_sentences])
        
        result = {
            'original_length': len(text.split()),
            'summary_length': len(summary.split()),
            'reduction_percentage': ((len(text.split()) - len(summary.split())) / len(text.split())) * 100,
            'summary': summary,
            'summary_type': summary_length,
            'key_sentences': [s[0] for s in top_sentences],
            'sentences_selected': len(top_sentences),
            'total_sentences': len(sentences)
        }
        
        return result
    
    def analyze_text(self, text: str) -> Dict:
        """Perform comprehensive text analysis"""
        print("🔍 Analyzing text content...")
        
        # Basic statistics
        words = word_tokenize(text)
        sentences = sent_tokenize(text)
        
        # Remove punctuation for word analysis
        clean_words = [w.lower() for w in words if w.isalnum()]
        
        # Calculate metrics
        analysis = {
            'basic_stats': {
                'character_count': len(text),
                'word_count': len(words),
                'sentence_count': len(sentences),
                'average_sentence_length': len(words) / len(sentences) if sentences else 0,
                'average_word_length': sum(len(w) for w in clean_words) / len(clean_words) if clean_words else 0
            },
            'readability': self._calculate_readability_metrics(text),
            'sentiment': self._analyze_sentiment(text),
            'vocabulary': {
                'unique_words': len(set(clean_words)),
                'lexical_diversity': len(set(clean_words)) / len(clean_words) if clean_words else 0,
                'most_common_words': self._get_most_common_words(clean_words, 10)
            },
            'complexity': {
                'flesch_reading_ease': self._flesch_reading_ease(text),
                'gunning_fog_index': self._gunning_fog_index(text),
                'automated_readability_index': self._automated_readability_index(text)
            }
        }
        
        # Generate recommendations
        analysis['recommendations'] = self._generate_text_recommendations(analysis)
        
        return analysis
    
    def translate_style(self, text: str, target_style: str) -> str:
        """Adapt text to different writing styles"""
        print(f"🔄 Adapting text to {target_style} style...")
        
        style_transformations = {
            'formal': {
                'patterns': [
                    (r"\bdon't\b", "do not"),
                    (r"\bcan't\b", "cannot"),
                    (r"\bwon't\b", "will not"),
                    (r"\bit's\b", "it is"),
                    (r"\bthat's\b", "that is"),
                    (r"\.\s+", ". Consequently, "),
                    (r"!\s+", ". It should be noted that ")
                ]
            },
            'casual': {
                'patterns': [
                    (r"do not", "don't"),
                    (r"cannot", "can't"),
                    (r"will not", "won't"),
                    (r"it is", "it's"),
                    (r"that is", "that's"),
                    (r"Furthermore, ", "Also, "),
                    (r"Consequently, ", "So ")
                ]
            },
            'academic': {
                'patterns': [
                    (r"\bshow\b", "demonstrate"),
                    (r"\btell\b", "illustrate"),
                    (r"\bmake\b", "construct"),
                    (r"\buse\b", "utilize"),
                    (r"\bbig\b", "substantial"),
                    (r"\bsmall\b", "negligible"),
                    (r"I think", "The evidence suggests")
                ]
            }
        }
        
        transformed = text
        
        if target_style in style_transformations:
            for pattern, replacement in style_transformations[target_style]['patterns']:
                transformed = re.sub(pattern, replacement, transformed, flags=re.IGNORECASE)
        
        return transformed
    
    def _combine_article(self, content: Dict) -> str:
        """Combine article parts into full text"""
        full_text = content['introduction'] + ' '
        full_text += ' '.join(content['body_paragraphs']) + ' '
        full_text += content['conclusion']
        return full_text
    
    def _calculate_readability(self, text: str) -> float:
        """Calculate simple readability score"""
        sentences = sent_tokenize(text)
        words = word_tokenize(text)
        
        if not sentences or not words:
            return 0
        
        avg_sentence_length = len(words) / len(sentences)
        avg_word_length = sum(len(w) for w in words if w.isalnum()) / len([w for w in words if w.isalnum()])
        
        # Simple readability formula (higher = more readable)
        readability = 100 - (avg_sentence_length * 2 + avg_word_length)
        return max(0, min(100, readability))
    
    def _calculate_readability_metrics(self, text: str) -> Dict:
        """Calculate various readability metrics"""
        sentences = sent_tokenize(text)
        words = word_tokenize(text)
        clean_words = [w for w in words if w.isalnum()]
        
        if not sentences or not clean_words:
            return {}
        
        return {
            'flesch_reading_ease': self._flesch_reading_ease(text),
            'gunning_fog_index': self._gunning_fog_index(text),
            'automated_readability_index': self._automated_readability_index(text),
            'coleman_liau_index': self._coleman_liau_index(text)
        }
    
    def _flesch_reading_ease(self, text: str) -> float:
        """Calculate Flesch Reading Ease score"""
        sentences = sent_tokenize(text)
        words = word_tokenize(text)
        syllables = sum(self._count_syllables(word) for word in words if word.isalnum())
        
        if not sentences or not words:
            return 0
        
        return 206.835 - 1.015 * (len(words) / len(sentences)) - 84.6 * (syllables / len(words))
    
    def _gunning_fog_index(self, text: str) -> float:
        """Calculate Gunning Fog Index"""
        sentences = sent_tokenize(text)
        words = word_tokenize(text)
        
        if not sentences or not words:
            return 0
        
        complex_words = sum(1 for word in words if word.isalnum() and self._count_syllables(word) >= 3)
        
        return 0.4 * ((len(words) / len(sentences)) + 100 * (complex_words / len(words)))
    
    def _automated_readability_index(self, text: str) -> float:
        """Calculate Automated Readability Index"""
        characters = sum(1 for char in text if char.isalnum() or char.isspace())
        words = len(text.split())
        sentences = len(sent_tokenize(text))
        
        if not sentences or not words:
            return 0
        
        return 4.71 * (characters / words) + 0.5 * (words / sentences) - 21.43
    
    def _coleman_liau_index(self, text: str) -> float:
        """Calculate Coleman-Liau Index"""
        characters = sum(1 for char in text if char.isalnum())
        words = len(text.split())
        sentences = len(sent_tokenize(text))
        
        if not sentences or not words:
            return 0
        
        L = (characters / words) * 100
        S = (sentences / words) * 100
        
        return 0.0588 * L - 0.296 * S - 15.8
    
    def _count_syllables(self, word: str) -> int:
        """Count syllables in a word (approximation)"""
        word = word.lower()
        count = 0
        vowels = "aeiouy"
        
        if word[0] in vowels:
            count += 1
        
        for i in range(1, len(word)):
            if word[i] in vowels and word[i-1] not in vowels:
                count += 1
        
        if word.endswith("e"):
            count -= 1
        
        if word.endswith("le") and len(word) > 2 and word[-3] not in vowels:
            count += 1
        
        if count == 0:
            count = 1
        
        return count
    
    def _analyze_sentiment(self, text: str) -> Dict:
        """Analyze text sentiment"""
        blob = TextBlob(text)
        
        return {
            'polarity': blob.sentiment.polarity,
            'subjectivity': blob.sentiment.subjectivity,
            'sentiment': self._categorize_sentiment(blob.sentiment.polarity),
            'confidence': abs(blob.sentiment.polarity)
        }
    
    def _categorize_sentiment(self, polarity: float) -> str:
        """Categorize sentiment based on polarity"""
        if polarity > 0.3:
            return "positive"
        elif polarity < -0.3:
            return "negative"
        else:
            return "neutral"
    
    def _get_most_common_words(self, words: List[str], n: int = 10) -> List[Dict]:
        """Get n most common words"""
        from collections import Counter
        word_counts = Counter(words)
        return [{'word': word, 'count': count} for word, count in word_counts.most_common(n)]
    
    def _generate_text_recommendations(self, analysis: Dict) -> List[str]:
        """Generate recommendations based on text analysis"""
        recommendations = []
        
        stats = analysis['basic_stats']
        readability = analysis['readability']
        
        # Sentence length recommendations
        avg_sentence = stats['average_sentence_length']
        if avg_sentence > 25:
            recommendations.append("Consider breaking long sentences into shorter ones for better readability.")
        elif avg_sentence < 10:
            recommendations.append("Vary sentence length to create more engaging rhythm.")
        
        # Readability recommendations
        if 'flesch_reading_ease' in readability:
            if readability['flesch_reading_ease'] < 50:
                recommendations.append("Text may be difficult to read. Consider simplifying vocabulary and sentence structure.")
            elif readability['flesch_reading_ease'] > 80:
                recommendations.append("Text is very readable, appropriate for general audiences.")
        
        # Vocabulary recommendations
        if analysis['vocabulary']['lexical_diversity'] < 0.5:
            recommendations.append("Consider using more varied vocabulary to enhance text quality.")
        
        # Word count recommendations
        if stats['word_count'] < 100:
            recommendations.append("Text is quite brief. Consider adding more detail or examples.")
        elif stats['word_count'] > 2000:
            recommendations.append("Text is very long. Consider breaking into sections or adding summaries.")
        
        return recommendations if recommendations else ["Text analysis shows good writing characteristics."]
    
    def get_writing_stats(self) -> Dict:
        """Get statistics about agent's writing activity"""
        return {
            'articles_generated': len([h for h in self.content_history if h['action'] == 'generate_article']),
            'total_words_written': sum(h.get('word_count', 0) for h in self.content_history),
            'first_activity': self.content_history[0]['timestamp'] if self.content_history else None,
            'last_activity': self.content_history[-1]['timestamp'] if self.content_history else None
        }

# Quick test
if __name__ == "__main__":
    writer = ContentWriterAgent()
    
    print("Testing Content Writer Agent...")
    
    # Test article generation
    article = writer.generate_article("Artificial Intelligence", style="technical")
    print(f"✅ Generated article: {article['title']}")
    print(f"   Word count: {article['word_count']}")
    print(f"   Readability: {article['readability_score']:.1f}/100")
    
    # Test summarization
    sample_text = """
    Artificial intelligence is transforming multiple industries through automation and data analysis. 
    Machine learning algorithms can process vast amounts of information to identify patterns and make predictions. 
    However, ethical considerations remain important when deploying AI systems. 
    Future developments will likely focus on explainable AI and human-AI collaboration.
    """
    
    summary = writer.summarize_text(sample_text, "brief")
    print(f"\n✅ Created summary: {summary['reduction_percentage']:.1f}% reduction")
    print(f"   Original: {summary['original_length']} words")
    print(f"   Summary: {summary['summary_length']} words")
    
    # Test text analysis
    analysis = writer.analyze_text(sample_text)
    print(f"\n✅ Text analysis complete")
    print(f"   Sentiment: {analysis['sentiment']['sentiment']}")
    print(f"   Recommendations: {len(analysis['recommendations'])}")
    
    print("\n🎉 Content Writer Agent is working!")
```
