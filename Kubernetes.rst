.. image:: https://www.vectorlogo.zone/logos/kubernetes/kubernetes-ar21.svg?effects=drop-shadow

Kubernetes:
-----------
- Kubernetes is an open-source container orchestration system for automating software deployment, scaling, and management. 
- Originally designed by Google, the project is now maintained by the Cloud Native Computing Foundation. 
- The name Kubernetes originates from Greek, meaning helmsman or pilot.

Basics of object management in Kubernetes.
--------------------------------------------
A Pod 
-------
The pod is the most important primitive in the API.  A pod runs your application inside of a container. 

1. To create pod - ``kubectl run``
2. Debugging misbehaving pods
    - ``kubectl get`` - Inspect pod status
    - ``kubectl decsribe`` - Inspect pod events
    - ``kubectl logs`` - Inspect pod logs
    - ``kubectl exec`` - Shell into pods for low-level information
3. Probes:  Containers running in a pod, can specify probes, to detect and correct failures. A probe is a small process, that runs periodically and reports a result.
    - Readiness probe: Containers running in a pod, can specify probes, to detect and correct failures. A probe is a small process, that runs periodically and reports a result.
    - Liveliness Probe: Once the application is up and running, we also want to know if the application is still functioning as expected. 
                        A liveness probe addresses this problem, and restarts the container if a problem has been detected
4. Multicontainer pods:  In the majority of cases, a pod runs an application in a single container. There are situations that call for operating more than a single container in a pod. 
    - Init contianer:   For example, you may want to configure the state of a pod before the main application container starts up. Those containers are called init containers. 
        - Init containers are similar to constructors or static blocks in object oriented programming. The logic run in init containers prepares the environment, 
        - Key takeaway, an init container runs and completes before the main application container even starts.
    - Sidecars container:  Containers running alongside the main application container are called sidecars. 
        - The sidecar enhances the logic of the main application container to implement the single responsibility principle. The application runs in a container, 
           the sidecar runs in another container, and provides crosscutting helper functionality without conflating the application logic. 
        - Any data exchange between the containers usually happens through a mounted volume. 
        - There are two specialized implementations of the sidecar pattern, the adapter pattern and the ambassador pattern.
            - Adapter pattern: pod with sidecar container and application container can directly interact with External service.
            - ambassador pattern: pod with sidecar container and application container interact indirectly with External service using proxy kind of service.

Kubernetes configuration:
--------------------------
Kubernetes offers more advanced concepts for configuring a pod and its containers.

In fact, many of those concepts are implemented as primitives with a dedicated purpose in mind. 

- This part of the curriculum covers the primitives:
    - Containers can configure environment variables to control runtime behavior. Spelling out individual environment variables for a container 
      quickly becomes burdensome and tedious, if you need to control those key value pairs across tens or hundreds of pods. 
      In practice, you will find that config maps and secrets can be consumed by a pod as environment verbal references, or by simply mounting them as a volume. 
      The primitives config map and secret allow for externalizing configuration data and injecting them into a pod.

        - config maps: Plain text key-value pairs
        - secrets:  base64 encoded key-value pairs
    - security context:  By default, containers run with the root user, meaning with absolute power to run commands and access the file system. 
                        In Kubernetes, you can configure roles and privileges for a container to reduce the risk of security breaches. 
                        You can define a security context on the pod and container level to enforce those rules. 
                        It is helpful to explore the different options in the Kubernetes documentation and experience their functionality in practice. 
    - resource requirements: Kubernetes administrators create namespaces to subdivide a cluster into isolated sections. Each pod running in the namespace can consume an unlimited amount 
                            of resources, like CPU memory, as long as the maximum capacity hasn't been reached. You can define an object of type resource quota to set proper constraints to prevent resource bottlenecks. 
                            Understanding how to create resource quotas and how to define resource boundaries for pods is an important part of the exam. Learn the differences between defining minimum and maximum resources 
                            declared for a pod. In the context of a namespace, practice exceeding the granted limits to see their effect.
    - service accounts: Kubernetes administrators create namespaces to subdivide a cluster into isolated sections. Each pod running in the namespace can consume an unlimited amount of resources, 
                        like CPU memory, as long as the maximum capacity hasn't been reached. You can define an object of type resource quota to set proper constraints to prevent resource bottlenecks. Understanding how to create 
                        resource quotas and how to define resource boundaries for pods is an important part of the exam. Learn the differences between defining minimum and maximum resources declared for a pod. In the context of 
                        a namespace, practice exceeding the granted limits to see their effect.

Pod design
-----------
The deployment is a Kubernetes primitive that helps with managing multiple identical pods. The portion pod design of the curriculum touches on all important aspects of a deployment like 
    - scaling the number of replicas and 
    - rolling out new versions
 
    - Labels are one of the underpinning concepts for querying and selecting other Kubernetes objects. You need to be familiar with managing labels, and the fundamental differences with annotations.
    -   ``Labels`` are key value pairs assigned to Kubernetes objects. They play an essential role for querying, sorting, and filtering objects. 
    -   On the surface, ``Annotations`` look very similar to labels. However, they only represent descriptive metadata without the ability to be used for queries. You will only need to understand how to create them.

 Deployments select a pod specification by a matching label. 

 - Understand the YAML structure of a deployment and know how to tweak its settings to scale the number of replicas. The scale command provides a convenient shortcut for changing replicas with the Kube control command. 
 - Explore the different auto scaling options and their corresponding primitives as an alternative to setting the number of replicas manually
 - Practice the rollout history from both angles, rolling out a new revision, and rolling back to a previous revision. A deployment automatically takes care of updating all replicas with the desired revision. 
 - Practice the creation of both primitives inside and out including their configuration options. This is where your experience with formulating a more complex bash command may come into play.
 - understand the differences between a ``pod``, a ``job``, and a ``cronjob``.The differences are:
    - Pod: Infinite process
    - Job: One time process
    - Cron Job: Periodic process

Services and networking
------------------------
 - An application is useless without end users having access to it. Services take on the role of providing a central network interface for exposing an application inside and outside of the Kubernetes cluster. 
 - The curriculum puts a strong emphasis on networking aspects. You will have to demonstrate your proficiency with creating different types of services, as well as defining access control for incoming and outgoing traffic with network policies. 
 - A pod exposes an IP address to allow network access. The problem is that the IP address isn't stable over time. Kubernetes assigns a new IP address whenever the pod is restarted. 
 - Services abstract the underlying IP address of a pod or a set of pods by exposing a single network interface with the help of labeled selection. 
 - For the exam, you should understand the nuances of services. It's very important to know these service types, ``ClusterIP``, ``NodePort`` and ``LoadBalancer``. The type has a detrimental effect on the accessibility of pods from inside and outside of the Kubernetes cluster. 
   Practice the creation of a service for both modes and verify the correct behavior.
 - Network policies implement security features for a set of pods. Think firewall rules. They control the traffic coming in and going out of a pod. 
 - At the bare minimum, make yourself familiar with the general structure of a ``network policy``, that is ingress/egress(which direction of traffic is allowed to container?), the pod selector(to which pod does the rule apply to), import restrictions(which pod is allowed to call?).

State Persisitance:
--------------------
 Containers do not persist data beyond the restart. That's the responsibility of a volume. You can mount volumes to a Pod, read from it, and write to it. 
 Two types of persistent stores:

 - Volumes:  
    - Volumes provide persistent storage, that outlives a container's lifespan. i,e. Restricted to container only.
    -  To use it, you have to first define the volume for the Pod, and then mount it to a directory of a container. 
    -  Volumes are particularly useful in the context of multi-container Pods. 
    -  Kubernetes offers a wide range of volume types. The most relevant to the exam are, 
        - emptyDir
        - ConfligMaps
        - Secrets
    - Practice the portions of a YAML manifest, that define amount of volume.

 - Persistent Volumes:  
    - Persistent volumes go beyond the scope of a volume. They persist data that outlives the container Pod, and even a node restart.
    - The persistent volume mounts a path on the host and defines the storage capacity. The claim requests a persistent volume, and consumes it.
    - You can think of the claim as the mediator between the Pod and the physical storage.
