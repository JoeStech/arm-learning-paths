---
review:
    - questions:
        question: >
            The KleidiAI performance improvements are noticeable in which type of benchmarks?
        answers:
            - int8 benchmarks
            - float32 benchmarks
            - mixed int4/int8 benchmarks
        correct_answer: 3                    
        explanation: >
            Performance improvements are only noticeable in the mixed int4/int8 benchmarks. These improvements are due to more efficient use of the Arm i8mm instructions when using int4 quantization.
               
    - questions:
        question: >
            What is MediaPipe?
        answers:
            - A cross-platform framework from Google AI Edge used for building multimodal applied ML pipelines
            - A library for efficient matrix multiplication on Arm processors
            - A tool for quantizing machine learning models
        correct_answer: 1          
        explanation: >
            MediaPipe abstracts away low-level details when building ML pipelines, allowing developers to quickly build and iterate on multimodal AI applications.



# ================================================================================
#       FIXED, DO NOT MODIFY
# ================================================================================
title: "Review"                 # Always the same title
weight: 20                      # Set to always be larger than the content in this path
layout: "learningpathall"       # All files under learning paths have this same wrapper
---
