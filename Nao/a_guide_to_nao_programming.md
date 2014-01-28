##A Guide to NAO Programming
Author: xtan@cs.wisc.edu | Version: 0.0.1


###What is this about?
This is a list of steps to learn how to program a NAO robot. Instead of writing a step by step guide, I decided to link to articles and programs you can read and learn from. The official reference manual for NAO is awesome, but a lot of important information is hidden inside, here is a list of stuff you need to know and some comments for items I think you need to know more.

###Guide
####1. Setting up NAO
* Using [Python](https://community.aldebaran-robotics.com/doc/1-14/dev/python/install_guide.html#python-install-guide)
* Using [C#](https://community.aldebaran-robotics.com/doc/1-14/dev/dotnet/index.html)

A downloadable version of the SDKs can be found at: [Resources](https://community.aldebaran-robotics.com/resources/) (Please get the username and password from the forum)

####2. NAO's Hello World
A basic program that uses the voice synthesizer on Nao to speak "Hello World"

* using [Python](https://community.aldebaran-robotics.com/doc/1-14/dev/python/making_nao_speak.html)
* using [C#](https://community.aldebaran-robotics.com/doc/1-14/dev/dotnet/index.html#hello-world-example)


####3.: Moving NAO
This is an example of controlling NAO to move forward using the ``Motion`` Module.

Example: [Python](https://community.aldebaran-robotics.com/doc/1-14/dev/python/making_nao_move.html)

#####Remember to Set Stiffness
remember to set the stiffness ``motion.setStiffnesses("Body", 1.0)`` before moving. In simple terms, stiffness is whether there is electric current flowing through the motors in NAO's body. To learn more about [Stiffness](https://community.aldebaran-robotics.com/doc/1-14/naoqi/motion/control-stiffness.html#control-stiffness)

#####Declaring Variables
In the documentation, parameters for methods are often ``const AL::ALValue&``, but python and C# does not have this type. In __Python__ ALValues are often just simple list [1.0,5.0], while in __C#__ is ``System.Collections.ArrayList``.

Another common parameter or return value in documentation is ``std::vector<string>``, this in __Python__ again is a simple list, while in __C#__ is ``System.Generics.List<System.String>``. To learn more about the [mapping between C++ types and other programming languages](https://community.aldebaran-robotics.com/doc/1-14/naoqi/stdtypes.html)

#####More Information
Here is also a good place to understand that while NAOqi API is available on __8__ languages(C#, Mathlab, Java ....), only 2 langauges(__C++ and Python__) can be run natively on the robot (means starting the program on the robot)

However, all API follows the similar structure and the same method call can be found on all platform. For example, the following is the one to one mapping of the same method from python to C#, notice the similarities

Python:

    motion = ALProxy("ALMotion","192.168.1.1",9559)
    motion.move(0.5,0.5,0.5)
    
C#:

	MotionProxy motion = new MotionProxy("192.168.1.1", 9559);
	motion.move(0.5,0.5,0.5)



####4. The Mysterious .post 
This is a python demo for running two different action at the same time. 

Example: [Python](https://community.aldebaran-robotics.com/doc/1-14/dev/python/making_nao_move.html)

The important idea here is using ``.post`` for parallel task. What the System do when they see ``.post`` is creating another thread that runs the action. Remember you can wait for the action using the ``.wait()`` commmand. You can learn more about parallel task [here](https://community.aldebaran-robotics.com/doc/1-14/dev/naoqi/index.html#blocking-and-non-blocking-calls). 


####5. Understanding difference between ``SetAngles`` and ``AngleInterpolationWithSpeed``
I put this issue quite high on the list because it is an issue that nearly every NAO programer in the Lab(A bit generalization) has faced.
#####What is the problem?
If you read the documentation on [SetAngles](https://community.aldebaran-robotics.com/doc/1-14/naoqi/motion/control-joint-api.html#ALMotionProxy::setAngles__AL::ALValueCR.AL::ALValueCR.floatCR) and [AngleInterpolationWithSpeed](https://community.aldebaran-robotics.com/doc/1-14/naoqi/motion/control-joint-api.html#ALMotionProxy::angleInterpolationWithSpeed__AL::ALValueCR.AL::ALValueCR.floatCR), you will notice they are trying accomplish the same thing. The difference is that ``Motion.SetAngles`` is a __Non-Blocking__ call and ``Motion.AngleInterpolation`` is a __Blocking__ call.
####What is the meaning of Blocking and Non-Blocking?
The documentation here is actually misleading. Instead of Blocking and Non-Blocking, what the documentation wanted to say is ``Motion.AngleInterpolationWithSpeed`` will lock the resource, while ``Motion.SetAngles`` will NOT.
For example, if the following code is run:

		Motion.post.AngleInterpolationWithSpeed('HeadYaw',2,1,true)
		Motion.AngleInterpolationWithSpeed('HeadYaw',-2,1,true)

What will happen is NAO's head will move to the position 2 first and then move to the postion -2. But if the following code is run:

		Motion.post.setAngles('HeadYaw',2,0.4)
		Motion.setAngles('HeadYaw',-2,0.4)
		
Instead of moving to position 2 first, the ALMotion will be updated and move to position -2 immediately.

####6. ALMemory
Nao is a machine and at every cycle Nao will update a list in the system. The system have around 1000++ different variables. A full list of keys can be found [here](#). These variables can be acccess with a function called ``ALMemory.getData(KEY)``. This function is important as it allows you to simulate events using polling.

A Good example is the example using Python: 
[Python Example](https://community.aldebaran-robotics.com/doc/1-14/dev/python/processing_data.html)


####7. Beyond here
The most important document: [<h1>User Manual for NAO</h1>](https://community.aldebaran-robotics.com/doc/1-14/naoqi/index.html#naoqi-api)
Here is a list of other 'stuff':

* [List of Joints and the limitation of each joint](https://community.aldebaran-robotics.com/doc/1-14/family/nao_h25/joints_h25.html#h25-joints)
*  [Running Code on Nao (Python only)](https://community.aldebaran-robotics.com/doc/1-14/dev/python/running_python_code_on_the_robot.html)
*  [Reacting to Events (Python only)](https://community.aldebaran-robotics.com/doc/1-14/dev/python/reacting_to_events.html#python-reacting-to-events)
*  Identify the Error with ``Motion.angleInterpolationBezier``
