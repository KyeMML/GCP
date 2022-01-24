# Dataflow

Recommended for new data procesing pipelines, unified batch and streaming
- serverless and fully managed
- auto-scaling, tyrransform-by-transform (adaptive)
- expertise: Apache Beam

## Apache Beam
- Pipelines
  - identifies the data to be processed and the actions to be taken on the data
- PCollections
  -  data is held in distributed data abstraction called PCollections
  -  immutable, any change in pipline ingests one PCollection and creates a new one, it does not change the incoming PCollection
- PTransforms
  -  action or code is contained in an abstraction called a PTransform
  -  handles input, transformation and output of the data
  -  data in PCollection is passed along pipeline from one PTransform to another
- Pipeline Runners
  -  analogous to container hosts such as google kubernetes engine
  -  identicle pipeline can be run on a local computer, VM or service like dataflow on the cloud.
  -  only differences are in scale and access in platform specific services
  -  services runner used to execute the code is the backend system

## Dataflow Pipeline Examples
To facilitate a simple dataflow:  
PCollection_input -> PTransform_1 -> PTransform_2 -> PTransform_3 -> PCollection_output  
```Python 3.0
PCollection_output = (PCollection_input | PTransform_1 | Ptransform_2 | PTransform_3)
```
