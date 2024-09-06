# 🌐 [Análisis de Datos de Salud Neonatal e Integración de Chatbot](https://huggingface.co/spaces/C2MV/Neonatos_Peru_Sala_Situacional)

Este repositorio proporciona una solución completa basada en Python para el análisis de datos de salud neonatal utilizando técnicas de aprendizaje automático e integración con un chatbot interactivo. El sistema emplea varios servicios externos, como Pinecone para la búsqueda vectorial, Gradio para la interfaz de usuario y Sentence Transformers para el modelado semántico, creando una herramienta robusta para la consulta y visualización de datos.

## Contenido del Repositorio
- **images/**: Carpeta que contiene imágenes asociadas al análisis de datos, usadas en los carruseles de la interfaz de usuario.
- **.gitattributes**: Archivo que configura el manejo de archivos grandes (LFS - Large File Storage).
- **README.md**: Archivo actual con la descripción del proyecto y su configuración.
- **Reporte.pdf**: Informe en formato PDF que contiene un análisis detallado de los datos de salud neonatal en Perú.
- **app.py**: Código fuente principal que contiene la lógica del chatbot, el análisis de datos y la interfaz gráfica con Gradio.
- **requirements.txt**: Lista de dependencias necesarias para ejecutar el proyecto.
- **video.mp4**: Video introductorio que se muestra en la interfaz gráfica.

## Características del Proyecto:
- **Análisis de Datos de Salud Neonatal**: El sistema procesa registros médicos relacionados con recién nacidos, enfocándose en diagnósticos, tratamientos y hospitalizaciones.
- **Chatbot Interactivo Basado en Gradio**: Una interfaz de chatbot en Gradio permite a los usuarios hacer preguntas y recibir respuestas en tiempo real, basadas en los datos vectorizados almacenados en Pinecone.
- **Visualizaciones Personalizables**: El proyecto utiliza Gradio para mostrar gráficos, imágenes y estadísticas relacionadas con la salud neonatal.

## Requisitos Previos:
Asegúrate de tener Python instalado y ejecuta el siguiente comando para instalar los paquetes necesarios:

```
pip install curl_cffi tqdm bitsandbytes tiktoken g4f pinecone-client pandas datasets sentence-transformers
```

## Descripción del Código

1. **Instalación de Dependencias**
   El código comienza instalando las bibliotecas necesarias con `os.system`:
   
   ```python
   import os
   os.system('pip install curl_cffi tqdm bitsandbytes tiktoken g4f pinecone-client pandas datasets sentence-transformers')
   ```
   Esto asegura que las dependencias necesarias como Pinecone, Gradio, Sentence Transformers y otras estén instaladas.

2. **Inicialización de API Keys y Modelos**
   Se configuran las claves de API y el modelo de embeddings de Pinecone:

   ```python
   PINECONE_API_KEY = "tu-api-key"
   PINECONE_ENVIRONMENT = "us-east-1"
   pc = Pinecone(api_key=PINECONE_API_KEY)
   ```

   Además, se carga un modelo de transformadores de oraciones para generar representaciones vectoriales de las consultas de texto:
   
   ```python
   embedding_model = SentenceTransformer('BAAI/bge-m3')
   ```

3. **Configuración de Gradio con Tema Personalizado**
   Se configura una interfaz de usuario con Gradio, utilizando un tema personalizado para los elementos visuales como botones, campos de entrada, y tablas de datos.

   ```python
   theme = gr.themes.Default(primary_hue="indigo", ...)
   ```

4. **Conexión a Pinecone y Creación de Índice**
   El código conecta a Pinecone y crea o selecciona un índice para almacenar los datos:

   ```python
   def connect_to_pinecone(index_name):
       index = pc.Index(index_name)
       index_stats = index.describe_index_stats()
       print("Conectado exitosamente al índice:", index_stats)
   ```

5. **Búsqueda Vectorial y Generación de Respuestas**
   Para buscar respuestas basadas en las consultas de los usuarios, se realiza una búsqueda vectorial en Pinecone, usando las representaciones de texto generadas por el modelo:

   ```python
   def vector_search(query):
       xq = embedding_model.encode(query)
       res = index.query(vector=xq.tolist(), top_k=3, include_metadata=True)
   ```

6. **Generación de Prompts y Respuestas**
   El código genera un contexto con los resultados de la búsqueda y crea un prompt que el chatbot utiliza para responder a las preguntas:

   ```python
   def create_prompt(query, contexts):
       prompt_start = "\n\nContexto:\n"
       prompt_end = f"\n\nPregunta: {query}\nRespuesta:"
   ```

7. **Visualización de Datos y Gráficos**
   La interfaz de usuario de Gradio muestra un conjunto de imágenes y gráficos predefinidos para la visualización de datos de salud neonatal.

   ```python
   gr.Markdown("### Carruseles de Imágenes")
   ```

   Se crean carruseles para visualizar datos como registros por departamento, costos de medicamentos, y más.

8. **Interacción con el Chatbot**
   El chatbot permite la interacción con los usuarios para consultas personalizadas basadas en los datos analizados:

   ```python
   def chat_function(message, history):
       search_results = vector_search(message)
       query_with_contexts = create_prompt(message, search_results)
       response = complete(query_with_contexts)
   ```

## Ejecución del Proyecto:
1. Clona este repositorio.
2. Instala las dependencias mencionadas.
3. Ejecuta el código principal en tu entorno local.
4. Accede a la interfaz gráfica mediante Gradio y empieza a interactuar con el chatbot y los datos visualizados.
