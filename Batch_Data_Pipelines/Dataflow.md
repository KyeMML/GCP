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
```Python
PCollection_output = (PCollection_input | PTransform_1 | Ptransform_2 | PTransform_3)
```  
  
To create a PCollection  
```Python
import apache_beam as beam

if __name__ == '__main__':
  with beam.Pipeline(argv=sys.argv) as p:
    (p
      | beam.io.ReadFromText('gs://...') # read input
      | beam.FlatMap(count_words) # apply transform
      | beam.io.WriteToText('gs://...') # write output
    )
  # end of with-clause: runs, stops the pipeline
```  
  
# Run a pipline on Dataflow  
```Python
import apache_beam as beam
opions = {'project': <project>,
          'runner': 'DataflowRnnner', # where to run
          'region': <region>,
          'setup_file': <setup.py file>}
pipline_options = beam.pipeline.PipelineOptions(flags=[],**options)
pipeline = beam.Pipeline(options = pipeline_options) # this creates the pipeline
```
