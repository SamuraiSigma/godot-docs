.. Generated automatically by doc/tools/makerst.py in Godot's source tree.
.. DO NOT EDIT THIS FILE, but the doc/base/classes.xml source instead.

.. _class_PhysicsBody2D:

PhysicsBody2D
=============

**Inherits:** :ref:`CollisionObject2D<class_collisionobject2d>` **<** :ref:`Node2D<class_node2d>` **<** :ref:`CanvasItem<class_canvasitem>` **<** :ref:`Node<class_node>` **<** :ref:`Object<class_object>`

**Inherited By:** :ref:`RigidBody2D<class_rigidbody2d>`, :ref:`StaticBody2D<class_staticbody2d>`, :ref:`KinematicBody2D<class_kinematicbody2d>`

**Category:** Core

Brief Description
-----------------

Base class for all objects affected by physics.

Member Functions
----------------

+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`add_collision_exception_with<class_PhysicsBody2D_add_collision_exception_with>`  **(** :ref:`Node<class_node>` body  **)**                   |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`int<class_int>`    | :ref:`get_collision_layer<class_PhysicsBody2D_get_collision_layer>`  **(** **)** const                                                             |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`bool<class_bool>`  | :ref:`get_collision_layer_bit<class_PhysicsBody2D_get_collision_layer_bit>`  **(** :ref:`int<class_int>` bit  **)** const                          |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`int<class_int>`    | :ref:`get_collision_mask<class_PhysicsBody2D_get_collision_mask>`  **(** **)** const                                                               |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`bool<class_bool>`  | :ref:`get_collision_mask_bit<class_PhysicsBody2D_get_collision_mask_bit>`  **(** :ref:`int<class_int>` bit  **)** const                            |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`remove_collision_exception_with<class_PhysicsBody2D_remove_collision_exception_with>`  **(** :ref:`Node<class_node>` body  **)**             |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`set_collision_layer<class_PhysicsBody2D_set_collision_layer>`  **(** :ref:`int<class_int>` layer  **)**                                      |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`set_collision_layer_bit<class_PhysicsBody2D_set_collision_layer_bit>`  **(** :ref:`int<class_int>` bit, :ref:`bool<class_bool>` value  **)** |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`set_collision_mask<class_PhysicsBody2D_set_collision_mask>`  **(** :ref:`int<class_int>` mask  **)**                                         |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| void                     | :ref:`set_collision_mask_bit<class_PhysicsBody2D_set_collision_mask_bit>`  **(** :ref:`int<class_int>` bit, :ref:`bool<class_bool>` value  **)**   |
+--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

Member Variables
----------------

- :ref:`int<class_int>` **collision_layer**
- :ref:`int<class_int>` **collision_mask**
- :ref:`int<class_int>` **layers**

Description
-----------

PhysicsBody2D is an abstract base class for implementing a physics body. All \*Body2D types inherit from it.

Member Function Description
---------------------------

.. _class_PhysicsBody2D_add_collision_exception_with:

- void  **add_collision_exception_with**  **(** :ref:`Node<class_node>` body  **)**

.. _class_PhysicsBody2D_get_collision_layer:

- :ref:`int<class_int>`  **get_collision_layer**  **(** **)** const

Return the physics layer this area is in.

.. _class_PhysicsBody2D_get_collision_layer_bit:

- :ref:`bool<class_bool>`  **get_collision_layer_bit**  **(** :ref:`int<class_int>` bit  **)** const

Return an individual bit on the collision mask.

.. _class_PhysicsBody2D_get_collision_mask:

- :ref:`int<class_int>`  **get_collision_mask**  **(** **)** const

Return the physics layers this area can scan for collisions.

.. _class_PhysicsBody2D_get_collision_mask_bit:

- :ref:`bool<class_bool>`  **get_collision_mask_bit**  **(** :ref:`int<class_int>` bit  **)** const

Return an individual bit on the collision mask.

.. _class_PhysicsBody2D_remove_collision_exception_with:

- void  **remove_collision_exception_with**  **(** :ref:`Node<class_node>` body  **)**

.. _class_PhysicsBody2D_set_collision_layer:

- void  **set_collision_layer**  **(** :ref:`int<class_int>` layer  **)**

Set the physics layers this area is in.

Collidable objects can exist in any of 32 different layers. These layers are not visual, but more of a tagging system instead. A collidable can use these layers/tags to select with which objects it can collide, using :ref:`set_collision_mask<class_PhysicsBody2D_set_collision_mask>`.

A contact is detected if object A is in any of the layers that object B scans, or object B is in any layer scanned by object A.

.. _class_PhysicsBody2D_set_collision_layer_bit:

- void  **set_collision_layer_bit**  **(** :ref:`int<class_int>` bit, :ref:`bool<class_bool>` value  **)**

Set/clear individual bits on the layer mask. This makes getting a body in/out of only one layer easier.

.. _class_PhysicsBody2D_set_collision_mask:

- void  **set_collision_mask**  **(** :ref:`int<class_int>` mask  **)**

Set a direction from which bodies can go through this one; that is, the passed vector is the normal of the pass-through side of the surface. If this value is different from (0,0), any movement within 90 degrees of the opposite of this vector is considered an valid movement. Set this direction to (0,0) to disable one-way collisions.

.. _class_PhysicsBody2D_set_collision_mask_bit:

- void  **set_collision_mask_bit**  **(** :ref:`int<class_int>` bit, :ref:`bool<class_bool>` value  **)**

Set how deep at most a body can be with respect to this one for the physics server to force it to a non-overlapping position, if it allows one-way collisions (see :ref:`body_set_one_way_collision_direction<class_PhysicsBody2D_body_set_one_way_collision_direction>`).


