---
label: Projects
rank: 4
rocket: ::icon{"Rocket Launch"}
---

# {{rocket}} Projects
## Research & Machine Learning

::columns
::col

::card{"Parking Occupancy Supervision Study", icon="visibility", href="https://github.com/guptadhruv-dev/parking-occupancy-supervision-study"}
A supervision-curve study tracing accuracy from zero labels to full supervision across 493,000 parking-space crops on an A100 cluster. The model surfaced annotation errors in a decade-old benchmark, which meant the reported accuracy was pessimistic and the model was right. 99.75% at full supervision, 92.7% with only 100 labels per class.

::badge{"PyTorch"} ::badge{"Computer Vision"} ::badge{"Semi-Supervised Learning"} ::badge{"ResNet"}
::end

::card{"Multi-Modal Computer Vision Pipeline", icon="hub"}
An end-to-end pipeline over 40,000+ images across five visual conditions, reaching 92% classification accuracy. PCA cut the feature space from 50,000 to 200 dimensions while keeping 95% of the variance, ResNet-50 transfer learning lifted clustering accuracy 35%, and a custom 3.2M-parameter CNN hit 0.08 validation loss within 50 epochs.

::badge{"PyTorch"} ::badge{"ResNet-50"} ::badge{"PCA"} ::badge{"t-SNE"} ::badge{"UMAP"} ::badge{"K-Means"} ::badge{"Transfer Learning"}
::end

::end

::col

::card{"Permutation-Invariant Vision", icon="function", href="https://github.com/guptadhruv-dev/permutation-invariant-vision"}
Permutation invariance built from symmetric functions, a mathematical guarantee rather than a hope. I proved it by collapsing three shuffled input variants onto a single point in embedding space, with PCA plots showing the geometry of the guarantee across nine trained models.

::badge{"PyTorch"} ::badge{"Model Architecture"} ::badge{"Computer Vision", color="info"} ::badge{"PCA"}
::end

::card{"Tessa", icon="auto_awesome"}
A Model Steerability Platform on iOS (native) for Google PaLM 2, exposing every inference-time decoding parameter through a custom UI so faculty, mentors, and students could feel model behavior shift in real time. Backed by an empirical study of how those parameters govern language generation. 85% query resolution rate, and winner of the MIT-WPU Research Excellence Award.

::badge{"Swift"} ::badge{"iOS (native)"} ::badge{"Google PaLM 2"} ::badge{"NLP"} ::badge{"LLMs"}
::end

::end

::end


## Product & Platforms

::columns

::col

::card{"MIT-WPU Connect", icon="school"}
The flagship campus application for MIT World Peace University, built with real-time updates and interactive features that lifted student engagement. Earned a formal Letter of Appreciation from the Director of IT and recognition from founder Dr. Vishwanath Karad and Mr. Rahul Karad.

::badge{"Swift"} ::badge{"iOS (native)"} ::badge{"Android"} ::badge{"Full-Stack"}
::end

::end

::col

::card{"NLC Bharat 2023 Infrastructure", icon="event"}
The complete IT infrastructure for the university's largest event, the National Legislature Conference Bharat 2023: a custom event-management application with real-time participant tracking, built and run for a major institutional conference.

::badge{"iOS (native)"} ::badge{"Real-time Systems"} ::badge{"Backend"}
::end

::end

::end


## More on GitHub
::columns
::col

::card{"Transcriber", icon="graphic_eq", href="https://github.com/guptadhruv-dev/transcriber"}
An audio and speech transcription pipeline.

::badge{"Python"} ::badge{"Speech-to-Text"}
::end
::end

::col
::card{"Optical Music Recognition", icon="music_note", href="https://github.com/guptadhruv-dev/optical-music-recognition"}
An OMR system that turns sheet music into machine-readable notation.

::badge{"Python"} ::badge{"Computer Vision"} ::badge{"OMR"}
::end
::end

::col
::card{"Skies Across Cultures", icon="public", href="https://github.com/guptadhruv-dev/skies-across-cultures"}
A cross-cultural data study.

::badge{"Python"} ::badge{"Data Analysis"}
::end
::end
::end