---
title: Persisting Habits
slug: persisting-habits
---


# Persistence #

In this tutorial we are going to delve into the world of saving data in your application! **Persistence** is a key component in what allows us to manage state for an application.
I understand how that can be confusing so let us add a picture for context.

<img src="https://hungrygopher.com/wp-content/uploads/2015/07/soy_sauce_packet.jpg" width="200" height="200" />

*Trust me this example is going somewhere*


In alot of ways persistence can be thought of as these soy sauce packets. The reason being is that we can think of persistence as a way for us to be able to store data or in this case soy sauce! I can trust these packets to store the sauce even if I am not using them. The only time that changes is if I open the packet, resulting in losing the contents of the packet. 

In more ways than one, persistence in our application acts the same way! We are trusting the application to store our data even after the user exits out of our application. The only time this changes is if the user deletes the application ... in other words, opening the soy sauce packet.

In our application Habitual, we want to be able to persist habits after the user exits the application or in other words save the user's habits.