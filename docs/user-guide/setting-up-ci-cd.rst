:desc: Set up a CI/CD pipeline to ensure that iterative improvements are tested and 
      deployed with minimum manual effort

.. _setting-up-ci-cd:

Setting up CI/CD
================

.. contents::
   :local:
   :depth: 2


Overview
--------

Continous Integration (CI) is the practice of merging code changes into a
master or release branch frequently, and automatically testing those code
changes before they are integrated. Continuous Deployment (CD) means
automatically deploying integrated changes to a staging or production
environment. Together, they allow for more frequent improvements to your
assistant with less manual effort expended on testing and deployment.

You might already have a preferred CI/CD tool. Many Git repository
hosting services like Github, Gitlab, and Bitbucket also provide their own CI/CD
tools that you can make use of, depending on where your code is hosted.

Continuous Integration
----------------------

When improving your assistant, you’ll make different kinds of 
`incremental updates <https://rasa.com/docs/rasa-x/user-guide/improve-assistant#improve-assistant>`_.
To be sure that these changes are helping your model instead of hurting it, you
should set up automatic testing via a continuous integration pipeline. Doing so
will give you the information and confidence necessary to deploy small changes
often, to keep improving your assistant over time based on real data.

.. contents::
   :local:

Test Data
~~~~~~~~~

Whenever you make an update to NLU data or stories, you should make sure to
also update any test data you have. For NLU data, you can simply reshuffle and
split your training data into train and test sets, or use cross-validation. For
end-to-end test cases or test stories, you will need to manually verify that
they are still correct and complete.

The testing recommendations below are cummulative, meaning tests for minor
updates should also be run for major and architectural updates, and tests for
major updates should also be run for architectural updates.

Testing Minor Updates
~~~~~~~~~~~~~~~~~~~~~

Every time you `make a minor iterative update <https://rasa.com/docs/rasa-x/user-guide/improve-assistant/#minor-updates>`_, 
such as adding more NLU data from your users or saving an unseen successful
story to your training data, you may not have the resources to run a full
comparison test to check model performance. However, you should always test
that, after making changes, your new model will still pass the basic baselines.
For example, you should check that your test cases that were previously
successful do not suddenly fail, and that you didn’t introduce any conflicting
stories.

Validate Data
#############

:ref:`Data validation <validate-files>` verifies if there are any mistakes or
major inconsistencies in your domain file, NLU data, or story data. If ``rasa
data validate`` results in errors, training a model will also fail. By
including the ``--fail-on-warnings`` flag, the command will also fail on
warnings about problems that won't prevent training a model, but might indicate
messy data, such as actions listed in the domain that aren't used in any
stories.

Validate Stories
################

:ref:`Story validation <test-story-files-for-conflicts>` checks if you have any
stories where different bot actions follow from the same dialogue history.
Conflicts between will prevent Rasa from learning the correct pattern. You can
run story validation by passing the ``--max-history`` flag to ``rasa data
validate``.


End-to-End Testing
##################

Creating :ref:`end-to-end test stories <end-to-end-testing>` is the best way to
have confidence in how your assistant will act in certain situations. These
stories, written in a modified story format, allow you to provide entire
conversations and test that a bot given this user input will act in the
expected manner. This is especially important as you start introducing more
complicated stories from user conversations.


Testing Major Updates
~~~~~~~~~~~~~~~~~~~~~

For `major updates <https://rasa.com/docs/rasa-x/user-guide/improve-assistant/#major-updates>`_ 
to your assistant, like merging two intents into one or changing the logic of
your stories, you should always run a full comparison test to check model
performance does not decrease. 

NLU Comparison
##############

To perform a full comparison of NLU model performance, you can either test 
your model on a test set, or using cross-validation. This can be a fairly
resource intensive test, so you can set this test to run only when a certain
tag (e.g. "NLU change") is present, or only when changes to NLU data or
the NLU pipeline were made.


Testing Architectural Updates
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you make an update that `alters the structure of your assistant <https://rasa.com/docs/rasa-x/user-guide/improve-assistant/#architectural-updates>`_,
like adding a custom component, you will first need to manually verify that
your assistant still works as expected from an end-user perspective. You should
also make sure that any tests you have defined as part of your CI pipeline are
still relevant and correct for your updated structure. 

Testing your Action code
~~~~~~~~~~~~~~~~~~~~~~~~

The test framework used test your action code will depend on how it is implemented.
Whichever method of testing your code you choose, you should include 
running those tests in your CI pipeline as well.

Continuous Deployment
---------------------

.. contents::
   :local:

Overview
~~~~~~~~



Deploying your Rasa Model
~~~~~~~~~~~~~~~~~~~~~~~~~

You should already have a trained model from running end-to-end testing 
in your CI pipeline. If you're using Rasa X, you can have this model 
posted to Rasa X in your CI pipeline
<>
You can make it so that your CI pipeline will automatically post a model to
Rasa X (you should already have a trained model from testing) on merge You can
also make it tag as production via api endpoints You shouldn’t do this if you
action code changed (?)
<>


Deploying your Action Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you're using a containerized deployment of your action server, you can 
automate building a new image and deploying a new image tag 
with each update to your action code. 


