# Vector Embeddings in RAG - Lab

## 📋 Overview

This lab provides a comprehensive, hands-on introduction to **Vector Embeddings** in the context of **Retrieval-Augmented Generation (RAG)** systems. The lab has been adapted from DeepLearning.AI's course materials for use in Google Colab with a production-ready structure.

## 🎯 Learning Objectives

By completing this lab, you will:

1. ✅ Understand how vector embeddings capture semantic meaning in text
2. ✅ Learn to use **cosine similarity** and **Euclidean distance** for comparing embeddings
3. ✅ Implement embeddings using transformer-based models
4. ✅ Visualize high-dimensional embeddings using PCA
5. ✅ Understand the limitations of context windows and truncation effects

## 🚀 Quick Start

### Option 1: Google Colab (Recommended)

1. Click the badge below to open directly in Google Colab:

   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/labs/01_vector_embeddings/Vector_Embeddings_RAG_Lab.ipynb)

2. The notebook will automatically install all required dependencies
3. Run cells sequentially from top to bottom

### Option 2: Local Jupyter Environment

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO/labs/01_vector_embeddings

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook Vector_Embeddings_RAG_Lab.ipynb
```

## 📦 Dependencies

The lab requires the following Python packages:

```
sentence-transformers>=2.2.0
torch>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
scikit-learn>=1.3.0
ipywidgets>=8.0.0
```

These are automatically installed when running the notebook in Google Colab.

## 📚 Lab Structure

### Section 1: Introduction
- **1.1 A Bit of Theory**: Understanding vector similarity metrics
- **1.2 The Framework**: The retrieval workflow in RAG systems

### Section 2: The Embedding Model
- **2.1 Introduction**: What are embedding models?
- **2.2 Loading the Model**: Using BAAI/bge-base-en-v1.5
- **2.3 Embeddings in Practice**: Generating and comparing embeddings
- **2.4 Visualizing Word Embeddings**: PCA dimensionality reduction

### Section 3: Embeddings and Input Size
- **3.1 An Example**: Understanding context window limitations and truncation

## 🔑 Key Concepts Covered

### Distance Metrics

#### Cosine Similarity
```python
similarity = (v1 · v2) / (||v1|| × ||v2||)
```
- Measures the **angle** between vectors
- Range: -1 to 1 (higher = more similar)
- Ignores magnitude, focuses on direction

#### Euclidean Distance
```python
distance = √(Σ(v1ᵢ - v2ᵢ)²)
```
- Measures **straight-line distance** between vectors
- Range: 0 to ∞ (lower = more similar)
- Considers both direction and magnitude

### Embedding Model

The lab uses **BAAI/bge-base-en-v1.5**:
- 768-dimensional embeddings
- 512 token context window
- Optimized for English text
- State-of-the-art performance on semantic similarity tasks

## 🎨 Visualizations

The lab includes several visualizations:

1. **2D Vector Plots**: Understanding cosine similarity vs. Euclidean distance
2. **PCA Embeddings**: Visualizing 768-dimensional embeddings in 2D
3. **Similarity Comparisons**: Side-by-side metric comparisons

## ⚠️ Important Findings

### Context Window Limitation

The lab demonstrates a critical finding:
- The model has a **512-token context window**
- Text beyond this limit is **completely ignored**
- This can lead to **loss of important information**

**Example from the lab:**
```python
# Full text (4000+ characters)
big_text_embedding = model.encode(big_text)

# Truncated text (3000 characters)
truncated_embedding = model.encode(big_text[:3000])

# Result: Embeddings are IDENTICAL!
np.array_equal(big_text_embedding, truncated_embedding)  # True
```

## 💡 Practical Applications

Understanding vector embeddings is crucial for:

- 🔍 **Semantic Search**: Finding relevant documents based on meaning
- 🤖 **Question Answering**: Retrieving context for LLM responses
- 📚 **Document Clustering**: Grouping similar documents
- 🏷️ **Content Recommendation**: Suggesting related content
- 🌐 **Multilingual Systems**: Cross-language information retrieval

## 🛠️ Production Considerations

When using embeddings in production:

1. **Chunking Strategy**: Break large documents into manageable pieces
2. **Overlap**: Use overlapping chunks to maintain context
3. **Metadata**: Store document metadata alongside embeddings
4. **Vector Database**: Use specialized databases (Pinecone, Weaviate, Chroma)
5. **Caching**: Cache frequently accessed embeddings
6. **Batch Processing**: Generate embeddings in batches for efficiency

## 📖 Further Learning

### Recommended Resources

1. **Sentence Transformers Documentation**  
   https://www.sbert.net/

2. **Hugging Face Model Hub**  
   https://huggingface.co/models?pipeline_tag=sentence-similarity

3. **Vector Database Comparison**  
   https://github.com/erikbern/ann-benchmarks

4. **RAG Paper (Original)**  
   https://arxiv.org/abs/2005.11401

### Next Steps in the Course

- **Lab 2**: Advanced chunking strategies for large documents
- **Lab 3**: Building a complete RAG system
- **Lab 4**: Vector databases and efficient retrieval
- **Lab 5**: Hybrid search (keyword + semantic)
- **Lab 6**: Production deployment and monitoring

## 🤝 Contributing

This lab is part of a learning repository. If you find issues or have improvements:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📝 Notes for Recruiters

This lab demonstrates:

- ✅ **Technical Understanding**: Deep knowledge of vector embeddings and similarity metrics
- ✅ **Practical Skills**: Hands-on implementation with industry-standard tools
- ✅ **Production Mindset**: Awareness of limitations and production considerations
- ✅ **Documentation**: Clear, professional documentation standards
- ✅ **Best Practices**: Code organization, testing, and visualization

## 📄 License

This lab is for educational purposes. Original content from DeepLearning.AI.

## 🙏 Acknowledgments

- **DeepLearning.AI**: Original course content
- **BAAI**: BGE embedding model
- **Sentence Transformers**: Library and framework
- **Hugging Face**: Model hosting and ecosystem

---

## 📊 Lab Statistics

- **Estimated Completion Time**: 60-90 minutes
- **Difficulty Level**: Beginner to Intermediate
- **Prerequisites**: Basic Python, understanding of vectors
- **Code Cells**: 20+
- **Visualizations**: 5+

---

**Created**: January 2026  
**Last Updated**: January 2026  
**Version**: 1.0.0

For questions or feedback, please open an issue in the repository.
