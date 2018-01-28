.. _doc_speech_to_text:

Speech to text
==============

This is a quick tutorial on how the *Speech to Text* module works, as well
as how to use it in Godot.

**Currently, the module works on the following platforms:**

- Windows;
- OS X;
- Unix (with **PulseAudio** or **ALSA** as requirements);
- iOS.

Speech recognition 101
----------------------

What is **speech recognition**, often referred to as *speech to text*
(STT)? As the latter name suggests, it is a process in which spoken words,
either through an audio file or through the user's microphone, are
analyzed and converted to text. Check the image below, which shows the
three main components of a generic STT process:

.. image:: /img/stt_diagram.png

The flowchart applies perfectly in our scenario: *human speaker* is the
player, *automatic STT device* is this module and *application host* is
the game!

Many methods exist to apply STT, such as neural networks and long
short-term memory (LSTN). The one we'll focus on is *Hidden Markov Models*
(HMM), which is used by **Pocketsphinx**, part of the CMUSphinx_ project.
This library is responsible for the actual STT stuff going on underneath
the module.

.. _CMUSphinx: https://cmusphinx.github.io

For those interested in the math theory behind HMM, `this document`__
provides a simple, great introduction to it. Also, for more details on how
*Pocketsphinx* works, I recommend `this page`__ to get started.

.. _HMM: http://di.ubi.pt/~jpaulo/competence/tutorials/hmm-tutorial-1.pdf
.. _Speech101: https://cmusphinx.github.io/wiki/tutorialconcepts

__ HMM_
__ Speech101_

Necessary files for STT
~~~~~~~~~~~~~~~~~~~~~~~

Typically, the *Speech to Text* module requires three things to work:

HMM directory
  Known simply as the *model*, it is a collection of files in a folder
  which statistically describe the language you want to work with.
  *CMUSphinx* has a few language models available for download `here`__.

.. _Models: https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models

__ Models_

Dictionary file
  A text file containing words in the desired language, along with their
  pronunciation. For example, for US English, **red** would have the
  following line in the dictionary file:

  ``red R EH D``

  For more on building dictionary files, check `this page`__.

.. _Dictionaries: https://cmusphinx.github.io/wiki/tutorialdict

__ Dictionaries_

Keywords file
  Although optional for *Pocketsphinx*, this file is used by the module
  for a more efficient speech recognition in a game environment. This is a
  text file where every line contains a word and its respective detection
  threshold. These words must exist in the dictionary file, of course! An
  example of a line in this file would be:

  ``red/1e-4/``

  After the word, isolate the threshold with ``/`` characters.

Using the module
----------------

As a simple tutorial to learn how to use the module, let's make a Godot
game which simply recognizes colors spoken by the user.

New classes!
~~~~~~~~~~~~

Let's take a quick look at the tools we have in hands for STT. If the
module was successfully added to Godot, the following classes should be
available:

:ref:`STTRunner <class_STTRunner>`
  This new node is the key component to use STT. It runs a thread that
  continuously recognizes keywords from the user.

:ref:`STTConfig <class_STTConfig>`
  A new type of resource. It holds the files/folders used for
  :ref:`STTRunner <class_STTRunner>` nodes.

:ref:`STTQueue <class_STTQueue>`
  The :ref:`STTRunner <class_STTRunner>` recognizes keywords, but where
  does it store them? The answer is an :ref:`STTQueue <class_STTQueue>`
  object, a special wrapper for a queue data structure.

:ref:`STTError <class_STTError>`
  Unfortunately, things can go wrong during speech recognition. Some
  methods of the above classes may return a special error value, which are
  constants treated by this singleton class.

Creating the main node
~~~~~~~~~~~~~~~~~~~~~~

I recommend creating an empty project for this tutorial.

The first step is creating a :ref:`STTRunner <class_STTRunner>` node
through the Godot UI. It can be the root node, for simplicity. Create an
empty script for it and we're ready to begin!

Preparing a configuration object
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create a :ref:`STTConfig <class_STTConfig>` object, which stores important
STT filenames. You can also do so through Godot UI
(:ref:`STTRunner <class_STTRunner>` nodes have a setter for that), but
we'll do everything with :ref:`doc_gdscript` for simplicity.

::

    # Outside _ready()
    var config = STTConfig.new()

As said before, we need three things for STT to work. Let's recall:

HMM directory
  For the sake of this tutorial, we'll use **US English**, so download the
  `en-us`__ one from *CMUSphinx*. I recommend creating a ``res://stt/``
  directory, and saving the model directory as ``res://stt/hmm/``.

.. _en-usModel: https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/US%20English/cmusphinx-en-us-ptm-5.2.tar.gz/download

__ en-usModel_

Dictionary filename
  *Pocketsphinx* uses one for US English `here`__. Save it as ``res://stt/en-us.dict``.

.. _en-usDictionary: https://raw.githubusercontent.com/cmusphinx/pocketsphinx/master/model/en-us/cmudict-en-us.dict

__ en-usDictionary_

Keywords filename
  Copy the below text and save it as ``res://stt/colors.kws``.

  ::

    red/1e-4/
    orange/1e-7/
    yellow/1e-7/
    green/1e-6/
    blue/1e-5/
    purple/1e-7/
    black/1e-6/
    white/1e-6/

With that done, we have to set these files through our configuration
object and initialize it through the :ref:`init() <class_STTConfig_init>`
method. Note that this method returns an :ref:`STTError <class_STTError>`
constant; if this value is different than ``STTError.OK``, then an error
occurred.

::

    func _ready():
        config.set_hmm_dirname("res://stt/hmm")
        config.set_dict_filename("res://stt/en-us.dict")
        config.set_kws_filename("res://stt/colors.kws")

        var err = config.init()
        if err != STTError.OK:
            print(STTError.get_error_string(err))
            return

Starting the STTRunner thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Our key component for STT, :ref:`STTRunner <class_STTRunner>`, requires
:ref:`STTConfig <class_STTConfig>` and :ref:`STTQueue <class_STTQueue>`
objects before it can be used. We already have the former from last
section, so let's create the latter and set them:

::

    # Outside _ready()
    var queue = STTQueue.new()
    var stt = STTRunner.new()

    # In _ready()
    stt.set_config(config)
    stt.set_queue(queue)

We can then start the STT thread with the runner's
:ref:`start() <class_STTRunner_start>` method. This method can also
return an error value.

::

    # In _ready()
    err = stt.start()
    if err != STTError.OK:
        print(STTError.get_error_string(err))
        return

Our script should currently look similar to the below code. Note that we
added a ``set_process(true)`` for our next and final step...

::

    extends STTRunner

    var stt = STTRunner.new()
    var config = STTConfig.new()
    var queue = STTQueue.new()

    func _ready():
        config.set_hmm_dirname("res://stt/hmm")
        config.set_dict_filename("res://stt/en-us.dict")
        config.set_kws_filename("res://stt/colors.kws")

        var err = config.init()
        if err != STTError.OK:
            print(STTError.get_error_string(err))
            return

        stt.set_config(config)
        stt.set_queue(queue)

        err = stt.start()
        if err != STTError.OK:
            print(STTError.get_error_string(err))
            return

        set_process(true)

Getting recognized keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~

With the thread running, all that's left is to print our keywords! We
can get them through the queue's :ref:`get() <class_STTQueue_get>` method.
I'd like to emphasize that this method returns an empty
:ref:`String <class_String>` if the queue is empty. It wouldn't change
anything in this tutorial, but for many cases it might be nice to check if
the queue is empty before doing anything.

Since this is something done repeatedly, the code will go in the
``_process()`` function:

::

    func _process(delta):
        if not queue.empty():
            print(queue.get())

Note that we don't check if the thread is still running in our function.
For safety, it would be wise to do so using the runner's
:ref:`running() <class_STTRunner_running>` method, and act according to
that.

Start a scene and check if the keyword colors appear on Godot's output
window as you speak them!

Important questions
-------------------

Here, I'll address a few important questions that may occur after reading
through the tutorial.

**Q: Which microphone does the Speech to Text module use?**

    It always uses the default system microphone. Ideally, it should be
    possible to change it during runtime, but I couldn't find a way to
    detect which microphones are active on the system, which would probably
    depend on the OS. Therefore, I chose this simpler solution.

**Q: Can the Speech to Text module do recognition on audio files?**

    No, it only works with continuous microphone speech coming directly
    from the user.

**Q: What happens if an error occurs in the runner thread?**

    The runner thread is halted when an error occurs, and an attribute stores
    the corresponding :ref:`STTError <class_STTError>` error value. To get
    this value, use the runner's
    :ref:`get_run_error() <class_STTRunner_get_run_error>` method.

**Q: Are the HMM directory, dictionary file or keywords file validated
when I set them?**

    Unfortunately, no. **:(** I couldn't find an easy way to validate the
    HMM directory files or the dictionary file. *Pocketsphinx* seems to
    detect errors when the config's :ref:`init() <class_STTConfig_init>`
    method is called, but its documentation doesn't show any way of doing
    this analysis previously. I could validate the keywords file,
    though... Maybe I'll do this for a future version.

**Q: How are STT config files managed by the module?**

    Any STT config files should typically be stored in the ``res://`` path.
    However, since *Pocketsphinx* does not recognize this Godot path
    convention, there is a need to copy these files to ``user://`` so their
    paths can be externally referenced.

    The files are copied there when :ref:`STTConfig's <class_STTConfig>`
    :ref:`init() <class_STTConfig_init>` method is called, and always
    overwrite previously existent files as a precaution. Note that STT
    config files in ``user://`` shouldn't be modified while the game is
    running!

    All STT-related files and directories in ``user://`` are deleted when
    the game is closed. This is done so that extra space isn't wasted
    on the system.
