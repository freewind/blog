---
layout: post
id: 2427
alias: xian-tdd-open-workshop
tags: 未分类
date: 2014-03-23 00:56:54
title: 参加了下午的TDD open workshop有收获
---

今天下午在西安办公室举行了面向大众的一个open workshop活动，主要是让参与者体验结对编程与TDD。我加入公司快一年了，还从没有机会参加过这样的活动，所以便过来。今天来的人不多，只有七八个人，跟宣传有点仓促有关，其中有一些学生，有一些在周围上班的人，还有一些培训机构的老师。

今天的活动让我收获很大，讲师仝(tong2)键很给力，很多困扰我已久的问题终于得到了解答，让我觉得非常开心。

在加入公司之前，我虽然听过TDD这个概念并且也写过很多单元测试，但其实并不是真的清楚应该怎么去写测试，很多时候是为写而写。在客户现场这段时间，我一边向同事学习，一边跟客户探讨，虽然对于测试有了不少经验，但还不足以向别人讲清楚，遇到一些复杂的情况自己也经常犯迷糊。

仝老师的选题是一道经典题目：猜数字游戏。这个题目经常被拿来作为TDD的培训，可能大家有听说过。仝老师并没有一次把题目全说出来，而是把它分成了几个小题，一步步让大家做，每道题都对应了一个测试方面的重点。

## 第一题

我们要在游戏机上玩一个猜数字游戏，机器会想好一个不重复的4位数让我们猜，然后检查我们的输入，最后输出一个形如`?A?B`这样的字符串。其中A左边的数字是指，你输入的数字中有多少个它有并且位置也一模一样，而B左边数字是指它有但位置不一样。

举个例子，假如游戏想到的数字是`1234`，你输入`5678`，则它输出`0A0B`。你输入`1678`，它输出`1A0B`；你输入`1243`，它输出`2A2B`。

我们要做的，就是要实现这样的一个函数，当然要通过先写测试把它驱动出来。

这个题目的难点在于：测试写多少才够？

如果把所有情况都考虑到，那么四个不重复的数字，就有几千种情况，而用户的输入也有几千种情况，全测显然是不可能的事情。后来我们缩小范围，打算按"?A?B"来分，比如"0A0B","0A1B",直到"4A0B"，每种想一个测试数据，但这样的话也会有十几个。伙伴说只测边界情况，比如"0A0B","4A0B","0A4B"，其它的再挑几个。但由于我们都拿不定主意，我索性把问题放大，如何便给每一种情况写了一个测试。代码如下：

    public class ComparatorTest {
        private Comparator comparator;
        @Before
        public void setup() {
           comparator = new Comparator();
        }

        @Test
        public void test0A0B() {
            String result = comparator.compare("1234", "5678");
            assertThat(result).isEqualTo("0A0B");
        }
        @Test
        public void test0A1B() {
            String result = comparator.compare("1234", "4678");
            assertThat(result).isEqualTo("0A1B");
        }
        @Test
        public void test0A2B() {
            String result = comparator.compare("1234", "3478");
            assertThat(result).isEqualTo("0A2B");
        }
        // here are some more tests
        // ...
        @Test
        public void test4A0B() {
            String result = comparator.compare("1234", "1234");
            assertThat(result).isEqualTo("4A0B");
        }
    }
    

    这里会定义十几个测试用例，每一个都非常简单，仅仅是数据和结果不同。伙伴提出了疑问，认为这样的测试实在太累人了，这么简单的逻辑要写这么多测试代码，太痛苦了。而且以后测试的维护成本也会很高，一旦需求变了，要从这么大堆代码里一一修改，更痛苦。

    在showcase时候，我们的代码作为成功的反面教材，很好的警示了大家。大家在仝老师的指导下，讨论了以下几个问题：

    ### 测试应该写多少才够

    对于这个题目，我们的第一种方案测的太多了，实际上只需要测边界条件和一两个普通条件即可。下面是一个比较好的例子：

    @Test
    public void should_output_0A0B_when_we_guess_5678_for_1234() {
        String result = comparator.compare("1234", "5678");
        assertThat(result).isEqualTo("0A0B");
    }

    @Test
    public void should_output_4A0B_when_we_guess_1234_for_1234() {
        String result = comparator.compare("1234", "1234");
        assertThat(result).isEqualTo("4A0B");
    }

    @Test
    public void should_output_0A4B_when_we_guess_2341_for_1234() {
        String result = comparator.compare("1234", "2341");
        assertThat(result).isEqualTo("0A4B");
    }

    @Test
    public void should_output_0A0B_when_we_guess_5678_for_1234() {
        String result = comparator.compare("1234", "5678");
        assertThat(result).isEqualTo("0A0B");
    }

    @Test
    public void should_output_2A1B_when_we_guess_1273_for_1234() {
        String result = comparator.compare("1234", "1273");
        assertThat(result).isEqualTo("2A1B");
    }
    

    可以看到，它只测了3种边界情况和一种普通情况，代码数量不多，方法的命名也很清楚。

    那么，这样够了吗？

    我们写单元测试，有一个重要的原因是用来防止自己犯低级错误的。我们不能把写实现代码的人当作我们的敌人，一定要把全部情况都测到，以防止他们在里面故意留下各种隐蔽的陷阱。测试写的再多可能也没有办法覆盖全部情况，所以只要能让自己感到安全即可。怎样才能让自己感到安全呢？这是没有标准答案的，只能是写多了测试以后慢慢体会。

    另外，写测试也要花时间的，比如`compare`这个方法的实现部分，我们只花了一两分钟就写完了，而这些测试代码，我们花了足足半个多小时，这样做值得吗？对于简单的业务逻辑来说，当然是不值得的，毕竟我们还很多工作等着做，老板花钱是为了我们的产品代码，而不是测试代码。

    再考虑一种情况，我要创业，想了一个点子，做了一个网站，我当然是想以最快的速度把它做成型让别人用。如果我在完全不知道人们会不会喜欢的时候，先花大量时间写测试，最后发现没人用只能丢掉，这些测试岂不是白写了。

    所以还是上面那句话：单元测试是让你提升自己对代码的信心的，只要你感觉安全可以继续开发时就够了，不是越多越好。

    所以对于这个题目，推荐的方案就已经足够了。

    再考虑一个场景，假设上面那个`compare`方法不是你写的，而是一个复杂的遗留系统的一部分，而你要对它进行重构，那么测试应该写多少？

    遗留系统的特点一般都是测试少，文档过时，需求没法追踪，只能通过已有代码反推需求。在这种情况下，想达到同样“安全”的感觉，势必需要补更多的测试，考虑更多的复杂情况，以保证我们重构之后不会破坏它原有的功能。

    所以，就算是同样的代码，在不同的场景下，我们需要写的测试数量也是不一样的，关键还是安全感。

    ### 测试方法应该怎么命名

    在看大家的代码时，我发现对于测试的方法，有三种不同的命名方式：

    public class ComparatorTest {
        public void test0A1B() {}
        public void should_output_0A1B_when_guess_5671_for_1234() {}
        public void should_output_0A1B() {}
    }
    

    哪种方式才对呢？

    第二种是比较推荐的，因为我们写测试的一个重要目的，是把它当作可执行的文档来用的，每一个测试通常会覆盖一个需求点，让以后维护的人知道你的产品代码实现了哪些需求。

    使用第二种方法，读起来像是一句话，比较流畅，也能够突出“在什么情况下应该做什么”，让人一看名字就知道这个测试在做什么。

    我们在写测试时，有时候会觉得命名没有那么重要，给个大概就行了，因为：

1.  这个需求点这么明显，一看产品代码就知道了，难道还会错？
2.  测试意图在测试代码里已经体现的很清楚了，直接看代码就行了

    对于第一点，开发的时候的确很清楚需求，但是几个月之后，甚至被别人接手修改后呢？产品代码可能已经被人修改过了，原作者很可能已经不记得当时为什么要做这个功能、写这些代码，而修改者只是在原来的代码上修修补补。最开始简单的代码慢慢变得复杂，直到让人难以理解。而当时单独写的文档可能已经过期，它描述的功能与实际的代码之间已经对应不上，最后接手的人不知道该相信文档还是相信代码。

    对于第二点，测试代码本身可能会比较复杂，让人难以一眼看明白逻辑。同时测试代码可能也会在多次修改之后，偏离了原本的意图。这个时候，通过读测试代码反推需求同样不可靠了。

    而如果我们在测试方法名上尽可能简洁而准确的表明“产品代码在什么情况下应该做什么”，同时测试代码和产品代码都以它作为基准，这样就可以把测试看成“可执行的文档”，长久保持有效性。以后维护的人要修bug或者修改什么功能，只需要找到测试，看看名字就能知道代码有什么功能，然后再看测试代码和产品代码，就能很快定位到修改点。

    所以我们在写测试时，不要想着自己是在写代码，而要想成是在写文档，如何保证它准确、可读才是最重要的。

    第二种方案，使用下划线分隔单词以提高可读性，在方法名中提供输入条件和预期结果以保证准确性，是比较好的方式。

    ### 是否使用0A0B

    关于测试名，仝老师还补充了一个问题：我们是否应该在测试名上使用如0A0B这样的词。

    仝老师说这个问题曾经在thoughtworks有过激烈的争论，因为有人认为应该尽量使用人们都能理解的词，而不是一些不懂业务就看不懂的词。比如这个0A0B，我们应该写成“既没有位置和内容都正确的数字，也没有内容正确和位置不对的数字”。

    后来争论的结果是，我们可以把它当作“业务名词”，是可以写在方法名里的。因为作为参与到项目中的开发者，是必须要理解这些业务名词的意思的。既然大家都理解，为什么还要想办法把它变成另一种说法呢？

    至少我觉得"0A0B"既好写又好懂，如果真要写成“既没有位置和内容都正确的数字，也没有内容正确和位置不对的数字”，我不知道自己能不能用英文写的出来，也不知道别人能不能看得懂。

    ### 我们可不可以准备好大量数据，在一个测试里循环验证

    我们其实还想出了一种方案：把数据都放在一个二维数组里，只需要写一个测试，循环一下就可以了。代码如下：

    public class ComparatorTest {
        private static final TEST_DATA = {
            {"1234", "5678", "0A0B"},
            {"1234", "5671", "0A1B"},
            {"1234", "5612", "0A2B"},
            // more data
            {"1234", "1234", "4A0B"},  
        };
        @Test
        public void testAll() {
            for(String[] testCase: TEST_DATA) {
                String result = comparator.compare(testCase[0], testCase[1]);
                assertThat(result).isEqualTo(testCase[2]);
            }
        }
    }
    

    伙伴对这个方案很满意，因为代码少了，而关键的测试数据都放在了一起，好查看好修改，以后要是需要添加新的测试数据，直接加一行就行了。

    这种做法是否推荐呢？

    仝老师的意思是，这主要看客户有没有这种大数据量测试的需求。如果有的话，当然可以采用这种方式，甚至还可以利用jUnit提供的“数据驱动”的功能，更好的实现：比如不需要手动写循环，哪一行出错还可以直接定位错误数据。

    但对于这道题目来说，使用前面推荐的方案就可以了。

    ## 第二题

    我们要写一个生成随机数的函数，并且要保证它是一个长度为4位的数字。

    这个题目的难点在于：如何测试随机数？

    我们很快完成了，测试代码如下（产品代码略）：

    public class NumberGeneratorTest {
        private NumberGenerator numberGenerator;
        @Before
        public void setup() {
            numberGenerator = new NumberGenerator();
        }
        @Test
        public void should_generate_numbers_with_length_of_4() {
            for(int i=0;i<10000;i++) {
                String number = numberGenerator.generate();
                assertThat(number.length()).isEqualTo(4);
            }
        }
        @Test
        public void should_generate_string_with_digits_only() {
            for(int i=0;i<10000;i++) {
                String number = numberGenerator.generate();
                // assert number contains digits only
            }
        }
        @Test
        public void should_generate_string_without_duplicate_characters() {
            for(int i=0;i<10000;i++) {
                String number = numberGenerator.generate();
                // assert number has no duplicate digits
            }
        }
    }
    

    对于这个题目，大家又一起讨论下面几个问题：

    ### 我们需要循环一万次吗

    在我们的测试里，每一个测试都把待测方法运行了一万次，以表明我们是在测试一个随机数，多测几次保险。而其它人的代码里，并没有这次循环，只测了一次。

    到底哪种更好一些呢？

    最后大家比较认同的是，只测一次就足够了，因为这些单元测试我们经常会遇到，如果有问题很容易发现。同时功能代码是我们自己写的，我们有信心自己会输出一个随机数，不测也可。另外，由于随机数的不确定性，我们很难在测试中保证它一定会满足某种条件，所以不太好写，不测也罢。

    但我的伙伴还是觉得只跑一次不可靠，因为如果算法比较复杂，运行多次更容易暴露出它的问题，所以倾向于在不影响性能的前提下，尽可能多跑。

    对于这个疑问，仝老师的意思是，多跑几次也没关系，加上就行了，有争论这些问题的时间早把功能完成了。对于这种小问题，我们一般采用“随便”的态度。

    ### 需要保证两次不相同吗

    仝老师又提出一个问题：如果用户来玩游戏，结果发现机器第二次想好的数跟第一次一样，会不会觉得很郁闷？我们要不要加个测试来证明它不会连续两次生成同一个数字吗？

    大家普遍表示这个测试没法写，因为随机数的不确定性，我们没法保证第二次跟第一次不一样。

    对于这个问题，仝老师的意思是，这时开发者应该向业务分析师提出这个问题，由业务分析师来决定。如果他们说要，那我们就要修改代码，保证第二次产生的跟前一次不同，并且加上相应测试；否则连测试都不用加了。

    仝老师说，从这个问题我们可以发现，业务需求并不总是由业务分析师发现的，开发者也有机会向他们提出建议和帮助。

    ## 第三题

    前面我们已经写了两个类，一个用于生成随机数，一个用于比较两个数字，现在要写一个新的类，把它们集成起来，就像胶水一样把它们两个的功能结合在一起，提供一个方法让用户只需要输入一个数字即可。

    这个题目的难点：如何对集成本身进行测试？

    这时候需要用mock了，在Java里一般使用mockito这个大杀器。

    我们的测试代码如下：

    public class GameTest {
        private NumberGenerator numberGenerator;
        private Comparator comparator;
        private Game game;
        @Before
        public void setup() {
            numberGenerator = mock(NumberGenerator.class);
            comparator = mock(Comparator.class);
            game = new Game(numberGenerator, comparator);
        }

        @Test
        public void should_output_0A0B_if_user_input_5678_when_answer_is_1234() {
            when(numberGenerator.generate()).thenReturn("1234");
            when(comparator.compare("1234", "5678")).thenReturn("0A0B");

            String result = game.guess("5678");
            assertThat(result).isEqualTo("0A0B");
        }
    }
    

    大家讨论了下面几个问题：

    ### 一个测试够了吗

    可以看到，这个测试只有一个用例，够了吗？

    由于这个测试测的是胶水类与其它类之间的交互，一个测试就可以覆盖。而“其它类”都有各自的测试代码，不需要在这里重复测。

    ### 构造函数

    我的伙伴提出了一个问题：`Game`这个类的构造函数居然要传入两个参数，这样做好吗？我们要不要再给它提供一个无参的构造函数，默认提供相应的对象，而把有参的仅仅作为测试用？

    就像下面这样：

    public class Game {
        private NumberGenerator numberGenerator;
        private Comparator comparator;
        public Game() {
            this(numberGenerator, comparator);
        }
        public Game(NumberGenerator numberGenerator, Comparator comparator) {
            this.numberGenerator = numberGenerator;
            this.comparator = comparator;
        }
    }
    

    仝老师使用反问来回答：做这样不好吗？我们为什么一定要提供一个无参的构造函数，为什么不能只提供一个有两个参数的构造函数？

    仝老师的观点是：我们没有证据来说哪个好哪个不好，而只提供一个有两个参数的构造函数这种方案，可以有更好的可测试性和可扩展性。提供无参构造函数，说明这个类会自己来管理生命周期，这样做是不好的，会对扩展性产生阻碍。而我们一般会使用如spring这样的工具，把这些生命周期的管理和对象之间的依赖放在外部，由容器管理。

    看起来构造函数里参数多了有点吓人，实际上使用了spring这样的容器后，我们基本上不需要手动调用它们，并不会带来麻烦。

    ### 需要测调用次数吗

    我提了一个问题：我们需要在测试中验证Game类对另外两个类的方法的调用次数吗？

    仝老师的意思是，如果我们没写，就表示调用一次。如果对调用次数有要求，则应该写。

    我感觉，对于这个例子来说，`numberGenerator.generate()`的调用次数是有要求的：在一次游戏中只应该生成一次答案，可以加上验证。而对于`comparator.compare(...)`，我们更关注的是它的返回值，所以没有必要验证。

    ### mock的分类

    仝老师还讲了一个mock的分类，大意是，曾经mock分为了很多种，比如stub, fake, dummy等等，它们之间都有微小的差别，我们在调用时还需要指定用哪个。但是后来人们发现，分这么细没用，没人关注你是什么，只关注你的行为，所以到最后都统一成mock了。

    ## 第四题

    再对题目进行扩展：现在要增加人机交互界面了，用命令行即可。机器会提示让用户输入一个数字，它检查并给出结果。如果输入的数字跟它预想的一样，直接输出"Congratulations!"，否则输出"?A?B"这样的提示。如果失败6次，则输出"You lose"并退出。

    这道题的重点在于：我们如何写一个集成测试来测试这些功能？

    由于当时我们在跟仝老师讨论一些类设计的问题，没做这个题，只能把一些讨论过的问题记录下来：

    ### 集成测试中怎么测随机数

    仝老师说在集成测试里，我们需要用一个mock来替代`NumberGenerator`，让我觉得非常惊讶。因为我以为集成测试里是不能用mock的，要尽量使用系统中真实的类。

    仝老师说：集成测试的目的是要测试不同单元之间的集成，重点在于交互时的行为，而不是用不用真实的类。如果你的系统依赖于一些复杂的外部环境，就像随机数这样不可控，你的集成测试怎么写？

    差不多明白了，因为想起在上个项目，为了测JMS queue，自己还是用了一些mock来替代真实的模块。

    ### Happy pass 就够了吗

    仝老师还提到，这个集成测试我们只需要写一个happy pass的用例就行了，我感到很奇怪：不是有赢和输两种情况吗？难道不是两个用例吗？

    仝老师说我们要测的是模块间的交互行为，只需要测happy pass就满足了，其它的情况在其它的测试里都已经覆盖了，没必要再测。

    ### 如何进行端对端的测试

    七夕同学问到如何进行“端对端的测试”，即针对输入和输出进行测试，直接替换掉`System.in`和`System.out`就行了吗？

    仝老师认为这种做法就可以了，再低就要考虑从操作系统层面入手了。

    这一点我还不太理解，因为我在想能否直接以脚本形式启动这个类，然后拦截输入输出进行验证，因为我上个项目就是这么做的，我感觉可行。

    ## 总结

    活动结束后，大家一起做了一个回顾，都觉得这个活动很好，不够好的地方主要在时间安排和讲解上，感觉比较紧张，有点跟不上。还有就是不少同学对Java不熟悉，只能看不能写。

    仝老师说这个活动一般是十个小时的，这次只安排了四个小时，的确有点紧张，因为一开始考虑参与者都是有一些经验的。

    总体来说，我觉得这个活动很成功，的确有很多收获，希望能多参加一些这样的活动，最后感谢仝老师和所有的参与者们。

    * * *

    更新：

    ## 参考代码

    参考代码：https://github.com/lxdcn/ut-workshop/tree/master/java/maven/src/test/java

    这是仝老师后来发过来的参考代码，有些地方值得注意：

    ### 随机数的测试

    看到一个很酷的测试：

    https://github.com/lxdcn/ut-workshop/blob/master/java/maven/src/test/java/GuessNumberTest.java#L40

    代码如下：

    /**
     * Real integrate test
     */
    @Test
    public void integrate_test_RandomNumberGenerate_and_CompareTwoNumbers_should_work() {
        Random random = mock(Random.class);
        when(random.nextInt(10)).thenReturn(1).thenReturn(2).thenReturn(3).thenReturn(4);

        RandomNumberGenerator randomNumberGenerator = new RandomNumberGenerator(random);
        CompareTwoNumbers compareTwoNumbers = new CompareTwoNumbers();

        GuessNumber guessNumber = new GuessNumber(compareTwoNumbers, randomNumberGenerator);
        assertThat(guessNumber.guess("1234")).isEqualTo("4A0B");
    }
    

    要以看到，作者创建的`RandomNumberGenerator`类还可以接受一个`Random`对象，这样通过对`Random`进行mock来测试随机数的生成，是一个很好的思路。

    这是一个"Real integrate test"。因为我们可以把`Random`类看作是一个外部单元，而我们的代码又是一个单元。通过指定`Random`的行为来对两个单元进行集成测试。

    ### 测随机数连续5次不同

    看到有一个测试，测连续5次生成的随机数不相同：

    https://github.com/lxdcn/ut-workshop/blob/master/java/maven/src/test/java/RandomNumberGeneratorTest.java#L57

    @Test
    public void numbers_should_not_duplicate_within_five_generates() {
        List<String> l = new ArrayList<String>();
        for (int i = 0; i < 5; i++) {
            l.add(randomNumberGenerator.generate());
        }
        for (int i = 0; i < l.size(); i++) {
            for (int j = 0; j < l.size() && i != j; j++) {
                assertThat(l.get(i)).isNotEqualTo(l.get(j));
            }
        }
    }

记得昨天说这个情况可以不用测，需要向BA确认。如果要测的话，我们还需要在代码中确保连续五次生成的随机数一定不相同。

另外发现，`GuessGame`这个类的功能好像还没有完成。
