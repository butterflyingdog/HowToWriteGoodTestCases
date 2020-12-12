.. default-role:: code

==================================================
如何使用Robot Framework编写好的测试案例
==================================================

.. contents:: Table of contents:
   :local:
   :depth: 2


简介
============

- 本文介绍了如何使用Robot框架编写好的测试用例的高级指南。

  - 如何与被测系统进行实际交互不在本文的讨论范围之内。

- 最重要的准则是让熟悉该领域的人尽可能容易理解测试用例。

  - 这通常也便于维护。

- 关于这个主题的更多信息，您可能想看看这些重要的资源：

  - `Robot Framework Dos and Don'ts`__ slides that are based on this how-to.
  - `Writing Maintainable Automated Acceptance Tests`__ article by Dale Emery.
  - `How to Structure a Scalable And Maintainable Acceptance Test Suite`__
    blog post by Andreas Ebbert-Karroum.

__ http://www.slideshare.net/pekkaklarck/robot-framework-dos-and-donts
__ http://cwd.dhemery.com/2009/11/wmaat
__ http://blog.codecentric.de/en/2010/07/how-to-structure-a-scalable-and-maintainable-acceptance-test-suite


命名规则
======

测试集合命名
----------------

- 测试集合名称应尽可能具有描述性。

- 可以从文件名或目录名自动创建测试集合名称，注意事项如下：
  
  - 不要包含文件扩展名。
  - 将下划线转换为空格。 
  - 如果名称都是小写，则单词将大写。

- 名称可以相对较长，但过长的名称不方便文件系统。

- 可以从命令行重写顶级测试集合的名称，必要时，请使用“--name”选项。

示例:

- `login_tests.robot` -> `Login Tests`
- `IP_v4_and_v6` -> `IP v4 and v6`

测试案例命名
---------------

- 测试案例名称应该像测试集合名称那样具有描述性。

- 如果一个测试集合包含许多类似的测试案例并且命名良好，测试案例名称可以更短。

- 测试案例名称与您在测试案例文件中指定的名称完全相同，无线任何名称转换。

例如，如果我们在一个文件中有与无效登录相关的测试`invalid_login.robot`，下边这些是正常的测试用例名称：

.. code:: robotframework

  *** Test Cases ***
  Empty Password
  Empty Username
  Empty Username And Password
  Invalid Username
  Invalid Password
  Invalid Username And Password

下边这些测试案例的名字就有些长了:

.. code:: robotframework

  *** Test Cases ***
  Login With Empty Password Should Fail
  Login With Empty Username Should Fail
  Login With Empty Username And Password Should Fail
  Login With Invalid Username Should Fail
  Login With Invalid Password Should Fail
  Login With Invalid Username And Invalid Password Should Fail


关键字命名
-------------

- 关键字名称应该是描述性的和清晰的。

- 关键字应该解释业务目的，而不是描述如何完成任务。

- 非常不同的抽象级别（例如“输入文本”或“管理员登录系统`）。

- 对于关键字是否应该是全标题大小写还是具有只有第一个字母大写。

	- 当关键字名称很短（例如“Input Text”）时，通常使用标题大小写。

	- 只将第一个字母大写通常对类似于句子的关键字更有效（例如“管理员登录系统”）。这些关键字的类型通常是更高级别的。

好的例子:

.. code:: robotframework

  *** Keywords ***
  Login With Valid Credentials

不好的例子:

.. code:: robotframework

  *** Keywords ***
  Input Valid Username And Valid Password And Click Login Button

  
案例初始化（setup）和清理（tearDown）阶段的命名
-------------------------

- 描述行为的目的。

  - 可以使用现有关键字。

- 如果初始化或清理阶段包含不相关的步骤，则可以使用更抽象的名称。

  - 不要在名称中列出步骤，这样是重复的，且不便于维护（例如“登录系统、添加用户、激活警报和检查余额”）。

  - 通常最好使用通用的描述（例如“初始化系统”）。

- 如果实现较低级别步骤的关键字已经存在，则使用内置关键字 `Run Keywords`__ 就可以很好地工作。

  - 不要重用，因此最好在只需要一次性初始化或清理的场景中使用。

- 使用这些测试案例的每个人都应该了解初始化或清理阶段的作用。  


Good:
.. code:: robotframework

  *** Settings ***
  Suite Setup     Initialize System

Good (if only used once):

.. code:: robotframework

  *** Settings ***
  Suite Setup     Run Keywords
  ...             Login To System    AND
  ...             Add User           AND
  ...             Activate Alarms    AND
  ...             Check Balance

Bad:

.. code:: robotframework

    *** Settings ***
    Suite Setup     Login To System, Add User, Activate Alarms And Check Balance

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Run%20Keywords


 
 
文档
=============

测试集合文档
------------------------

- 通常将整个文档添加到测试用例文件中是一个好主意。

- 应该包含背景信息，为什么要创建测试，关于执行环境等。

- 不要重复测试集合的名称。

  - 如果不是真的需要，最好没有文档。

- 不要包含太多关于测试用例的细节。

  - 测试应该足够清楚，可以单独理解。
  - 重复的信息既带来浪费，又不便维护。

- 文档可以包含指向更多信息的链接。

- 如果需要记录信息，请考虑使用元数据来表达，元数据可以表示为名称-值对（例如“Version:1.0”或“OS:Linux”）。

- 最顶级的测试集合的文档和元数据可以从分别使用“-doc”和“--metadata”选项的命令行。  

Good:

.. code:: robotframework

  *** Settings ***
  Documentation    Tests to verify that account withdrawals succeed and
  ...              fail correctly depending from users account balance
  ...              and account type dependent rules.
  ...              See http://internal.example.com/docs/abs.pdf
  Metadata         Version    0.1

Bad (especially if suite is named well like `account_withdrawal.robot`):

.. code:: robotframework

  *** Settings ***
  Documentation    Tests Account Withdrawal.


 

测试用例文档
-----------------------

- 测试通常不需要文档。

	- 案例所在测试集合的名称和可能的文档以及测试案例本身的名称应该提供足够的背景信息。

	- 测试用例结构应该足够清晰，尽量没有文档或其他注释。

- 推荐使用标签(Tag)，因为标签通常比文档更灵活，提供更多的功能（统计信息、包含/排除等）。

- 有时测试文档是有用的，确实有必要时可以使用它。

好的示例:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      [Tags]    Iteration-3    Smoke
      Open Login Page
      Input Username    ${VALID USERNAME}
      Input Password    ${VALID PASSWORD}
      Submit Credentials
      Welcome Page Should Be Open

不好的示例:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      [Documentation]    Opens a browser to login url, inputs valid username
      ...                and password and checks that the welcome page is open.
      ...                This is a smoke test. Created in iteration 3.
      Open Browser    ${URL}    ${BROWSER}
      Input Text    field1    ${UN11}
      Input Text    field2    ${PW11}
      Click Button    button_12
      Title Should Be    Welcome Page


	
用户自定义关键字中的文档
--------------------------

- 如果关键字相对简单，则不需要。

  - 好的关键字，包括参数名和清晰的结构就足够了。

- 关键字文档主要用于记录参数和返回值。

- 关键字文档可以在由 `Libdoc`__ 生成的资源文件和诸如 `RIDE`__ 这样的编辑器工具中显示。 

__ http://robotframework.org/robotframework/#built-in-tools
__ https://github.com/robotframework/RIDE


测试套件结构
====================

- 套件中的测试应该相互关联。

  - 推荐使用公共的初始化和/或清理步骤。

- 不应该在一个文件中有太多的测试（最多10个），除非它们是 `数据驱动测试`_。

- 测试案例原则上应该是独立的。可以使用setup/teardown初始化。

- 有时测试之间的依赖性是无法避免的。

  - 例如，单独初始化所有测试可能需要太多时间。

  - 从来没有长链的依赖性测试。

  - 考虑使用内置的`${PREV TEST STATUS}`变量来验证前一个测试案例的执行结果状态。

Test case structure
===================

- Test case should be easy to understand.

- One test case should be testing one thing.

  - *Things* can be small (part of a single feature) or large (end-to-end).

- Select suitable abstraction level.

  - Use abstraction level consistently (single level of abstraction principle).
  - Do not include unnecessary details on the test case level.

- Two kinds of test cases:

  - `Workflow tests`_
  - `数据驱动测试`_


Workflow tests
--------------

- Generally have these phases:

  - Precondition (optional, often in setup)
  - Action (do something to the system)
  - Verification (validate results, mandatory)
  - Cleanup (optional, always in teardown to make sure it is executed)

- Keywords describe what a test does.

  - Use clear keyword names and suitable abstraction level.
  - Should contain enough information to run manually.
  - Should never need documentation or commenting to explain what the test does.

- Different tests can have different abstraction levels.

  - Tests for a detailed functionality are more precise.
  - End-to-end tests can be on very high level.
  - One test should use only one abstraction level

- Different styles:

  - More technical tests for lower level details and integration tests.
  - "Executable specifications" act as requirements.
  - Use domain language.
  - Everyone (including customer and/or product owner) should always understand.

- No complex logic on the test case level.

  - No for loops or if/else constructs.
  - Use variable assignments with care.
  - Test cases should not look like scripts!

- Max 10 steps, preferably less.

Example using "normal" keyword-driven style:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      Open Browser To Login Page
      Input Username    demo
      Input Password    mode
      Submit Credentials
      Welcome Page Should Be Open

Example using higher level "gherkin" style:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      Given browser is opened to login page
      When user "demo" logs in with password "mode"
      Then welcome page should be open

See the `web demo project <https://github.com/robotframework/WebDemo/>`_
for executable versions of the above examples.

数据驱动测试
-----------------

- One high-level keyword per test.

  - Different arguments create different tests.
  - One test can run the same keyword multiple times to validate multiple
    related variations

- If the keyword is implemented as a user keyword, it typically contains
  a similar workflow as `workflow tests`_.

  - Unless needed elsewhere, it is a good idea to create it in the same file
    as tests using it.

- Recommended to use the *test template* functionality.

  - No need to repeat the keyword multiple times.
  - Easier to test multiple variations in one test.

- Possible, and recommended, to name column headings

- If a really big number of tests is needed, consider generating them based
  on an external model.

Example:

.. code:: robotframework

  *** Settings ***
  Test Template         Login with invalid credentials should fail

  *** Test Cases ***    USERNAME             PASSWORD
  Invalid Username      invalid              ${VALID PASSWORD}
  Invalid Password      ${VALID USERNAME}    invalid
  Invalid Both          invalid              invalid
  Empty Username        ${EMPTY}             ${VALID PASSWORD}
  Empty Password        ${VALID USERNAME}    ${EMPTY}
  Empty Both            ${EMPTY}             ${EMPTY}

  *** Keywords ***
  Login with invalid credentials should fail
      [Arguments]    ${username}    ${password}
      Input Username    ${username}
      Input Password    ${password}
      Submit Credentials
      Error Page Should Be Open

The `web demo project`_ contains an executable version of this example too.


User keywords
=============

- Should be easy to understand.

  - Same rules as with workflow tests.

- Different abstraction levels.

- Can contain some programming logic (for loops, if/else).

  - Especially on lower level keywords.
  - Complex logic in libraries rather than in user keywords.


Variables
=========

- Encapsulate long and/or complicated values.

- Pass information from them command line using the `--variable` option.

- Pass information between keywords.


Variable naming
---------------

- Clear but not too long names.

- Can use comments in variable table to document them more.

- Use case consistently:

  - Lower case with local variables only available inside a certain scope.
  - Upper case with others (global, suite or test level).
  - Both space and underscore can be used as a word separator.

- Recommended to also list variables that are set dynamically in the variable
  table.

  - Set typically using BuiltIn keyword `Set Suite Variable`__.
  - The initial value should explain where/how the real value is set.

Example:

.. code:: robotframework

  *** Settings ***
  Suite Setup       Set Active User

  *** Variables ***
  # Default system address. Override when tested agains other instances.
  ${SERVER URL}     http://sre-12.example.com/
  ${USER}           Actual value set dynamically at suite setup

  *** Keywords ***
  Set Active User
      ${USER} =    Get Current User    ${SERVER URL}
      Set Suite Variable    ${USER}

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Set%20Suite%20Variable


Passing and returning values
----------------------------

- Common approach is to return values from keywords, assign them to variables
  and then pass them as arguments to other keywords.

  - Clear and easy to follow approach.
  - Allows creating independent keywords and facilitates re-use.
  - Looks like programming and thus not so good on the test case level.

- Alternative approach is storing information in a library or using the BuiltIn
  `Set Test Variable`__ keyword.

  - Avoid programming style on the test case level.
  - Can be more complex to follow and make reusing keywords harder.

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Set%20Test%20Variable

Good:

.. code:: robotframework

  *** Test Cases ***
  Withdraw From Account
      Withdraw From Account    $50
      Withdraw Should Have Succeeded

  *** Keywords ***
  Withdraw From Account
      [Arguments]    ${amount}
      ${STATUS} =    Withdraw From User Account    ${USER}    ${amount}
      Set Test Variable    ${STATUS}

  Withdraw Should Have Succeeded
      Should Be Equal    ${STATUS}   SUCCESS

Not so good:

.. code:: robotframework

  *** Test Cases ***
  Withdraw From Account
      ${status} =    Withdraw From Account    $50
      Withdraw Should Have Succeeded    ${status}

  *** Keywords ***
  Withdraw From Account
      [Arguments]    ${amount}
      ${status} =    Withdraw From User Account    ${USER}    ${amount}
      [Return]    ${status}

  Withdraw Should Have Succeeded
      [Arguments]    ${status}
      Should Be Equal     ${status}    SUCCESS


Avoid sleeping
==============

- Sleeping is a very fragile way to synchronize tests.

- Safety margins cause too long sleeps on average.

- Instead of sleeps, use keyword that polls has a certain action occurred.

  - Keyword names often starts with `Wait ...`.
  - Should have a maximum time to wait.
  - Possible to wrap other keywords inside the BuiltIn keyword
    `Wait Until Keyword Succeeds`__.

- Sometimes sleeping is the easiest solution.

  - Always use with care.
  - Never use in user keywords that are used often by tests or other keywords.

- Can be useful in debugging to stop execution.

  - `Dialogs library`__ often works better.

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Wait%20Until%20Keyword%20Succeeds
__ http://robotframework.org/robotframework/latest/libraries/Dialogs.html
