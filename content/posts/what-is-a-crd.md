---
title: Quick example - CRD
date: 2022-04-15
description: 'Short example for a CRD'
---

## The Goal

We want to test our Camunda Platform 8 stack and benchmark it under the same conditions.

## Naive Approach

We start a *handful* of processes, we start *some* workers and we do that for an *arbitrary* duration.

## The Problem

Its hard to have that under the same conditions.

## The Solution

An instance of the custom resource: 
```yaml
apiVersion: cloud.camunda.io/v1alpha1
kind: Benchmark
metadata:
  name: benchmark-sample
spec:
  credentialsSecretName: cloud-credentials
  workerCount: 2
  processStarterRate: 200
  starterReplicas: 3
  duration: 1m30s
```

Now we can apply this to our Kubernetes and run the same configuration in a more controlled way.

## Behind the Scenes

You can checkout the implementation of the Operator on Github: https://github.com/Sijoma/camunda-benchmark-operator.

The custom resource definition (CRD).
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  annotations:
    controller-gen.kubebuilder.io/version: v0.8.0
  creationTimestamp: null
  name: benchmarks.cloud.camunda.io
spec:
  group: cloud.camunda.io
  names:
    kind: Benchmark
    listKind: BenchmarkList
    plural: benchmarks
    shortNames:
    - bench
    singular: benchmark
  scope: Namespaced
  versions:
  - additionalPrinterColumns:
    - jsonPath: .spec.workerCount
      name: '# Workers'
      type: string
    - jsonPath: .spec.starterReplicas
      name: '# Starters'
      type: string
    - jsonPath: .spec.processStarterRate
      name: Starter Rate
      type: string
    - jsonPath: .spec.duration
      name: Duration
      type: string
    - jsonPath: .metadata.creationTimestamp
      name: Age
      type: date
    - jsonPath: .status.progress
      name: Progress
      type: string
    name: v1alpha1
    schema:
      openAPIV3Schema:
        description: Benchmark is the Schema for the benchmarks API
        properties:
          apiVersion:
            description: 'APIVersion defines the versioned schema of this representation
              of an object. Servers should convert recognized schemas to the latest
              internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
            type: string
          kind:
            description: 'Kind is a string value representing the REST resource this
              object represents. Servers may infer this from the endpoint the client
              submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
            type: string
          metadata:
            type: object
          spec:
            description: BenchmarkSpec defines the desired state of Benchmark
            properties:
              credentialsSecretName:
                description: This has to be the name of a secret that contains clientId,
                  clientSecret, zeebeAddress and authServer. It defaults to "cloud-credentials"
                type: string
              duration:
                description: How long the benchmark should run
                type: string
              processStarterRate:
                description: Starter rate value on the starter deployment
                type: integer
              starterReplicas:
                description: Number of replicas for the starter
                type: integer
              workerCount:
                description: Number of workers
                type: integer
            required:
            - duration
            - processStarterRate
            - starterReplicas
            - workerCount
            type: object
          status:
            description: BenchmarkStatus defines the observed state of Benchmark
            properties:
              progress:
                type: string
              startTime:
                format: date-time
                type: string
            required:
            - progress
            - startTime
            type: object
        type: object
    served: true
    storage: true
    subresources:
      status: {}
status:
  acceptedNames:
    kind: ""
    plural: ""
  conditions: []
  storedVersions: []
```