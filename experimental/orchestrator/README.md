# Orchestrator (PREVIEW)

Orchestrator is [transformer][4] based technology that is heavily optimized for conversational AI applications. Orchestrator is built ground-up to run locally with your bot with no additional service calls required.

Conversational AI applications today are built using disparate technologies to fulfill language understanding (LU) needs – e.g. [LUIS][1], [QnA Maker][2]. Often, conversational AI applications are also built by assembling different [skills][3] each of which fulfill a specific conversation topic and can be built using different LU technologies. Hence, conversational AI applications typically require LU arbitration/ decision making to route incoming user request to an appropriate skill or to dispatch to a specific sub-component. Orchestration refers to the ability to perform LU arbitration/ decision making for a conversational AI application.  

## Supported scenarios
1. **Dispatch**: Orchestrator is a successor to [dispatch][5]. You can use Orchestrator instead of the current [dispatch][5] solution to arbitrate across your [LUIS][1] and [QnA Maker][2] applications. With Orchestrator, you are likely to see  
- improved classification accuracy
- higher resilience to data imbalance across your LUIS and QnA Maker authoring data.
- ability to correctly dispatch from relatively little authoring data.
2. **Recognizer**: You can use Orchestrator as a recognizer with [Adaptive dialogs][6]. 

## Authoring experience

Here's the end to end authoring experience.

<img src="./docs/media/authoring.png" style="align:center; width:300px;" />

Using [BF CLI][7]

1. [Export your LUIS application][8] and [convert to .lu format][9] or [export your QnA Maker KB to .qna format][10] 
2. Use `bf orchestrator:nlr:get` to download the NLR model.
3. Use `bf orchestrator:create` or `bf orchestrator:build` to generate the snapshot content.

## Runtime integration

1. For use in dispatch scenario, you can simply create `OrchestratorRecognizer` and provide it the path to the model as well the snapshot. You can then use the `Recognize` method to have Orchestrator recognize the user input

```C# 
string modelPath = Path.GetFullPath(OrchestratorConfig.ModelPath);
string snapshotPath = Path.GetFullPath(OrchestratorConfig.SnapshotPath);
OrchestratorRecognizer orc = new OrchestratorRecognizer()
{
    ModelPath = modelPath,
    SnapshotPath = snapshotPath
};

// To recognize user input
var recoResult = await orc.RecognizeAsync(turnContext, cancellationToken);
var topIntent = recoResult.Intents.First().Key;            
```

```js
const dispatchRecognizer = new OrchestratorRecognizer(process.env.ModelPath, process.env.SnapShotPath);
// To recognize user input
const recoResult = await dispatchRecognizer.recognizeAsync(context);
```

2. For use in adaptive dialogs, you can simply set the `recognizer` to `OrchestratorAdaptiveRecognizer`

```C#
string modelPath = Path.GetFullPath(OrchestratorConfig.ModelPath);
string snapshotPath = Path.GetFullPath(OrchestratorConfig.SnapshotPath);
const myDialog = new AdaptiveDialog()
{
    Recognizer = new OrchestratorAdaptiveRecognizer()
    {
        ModelPath = modelPath,
        SnapshotPath = snapshotPath
    }
}
```

```js
const myDialog = new AdaptiveDialog('myDialog').configure({
    recognizer: new OrchestratorAdaptiveRecognizer().configure(
    {
        modelPath: new StringExpression(process.env.ModelPath),
        snapshotPath: new StringExpression(process.env.RootDialogSnapshotPath),
    });
})
```

## Additional reading
- [API refrence][14]
- [BF CLI Orchestrator plugin][11]
- [C# samples][12]
- [NodeJS samples][13]

[1]:https://luis.ai
[2]:https://qnamaker.ai
[3]:https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-skills-overview?view=azure-bot-service-4.0
[4]:https://en.wikipedia.org/wiki/Transformer_(machine_learning_model)
[5]:https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=cs
[6]:https://aka.ms/adaptive-dialogs
[7]:https://github.com/microsoft/botframework-cli
[8]:https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisversionexport
[9]:https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert
[10]:https://github.com/microsoft/botframework-cli/tree/master/packages/qnamaker#bf-qnamakerkbexport
[11]:https://github.com/microsoft/botframework-cli/tree/beta/packages/orchestrator
[12]:./csharp_dotnetcore
[13]:./javascript_nodejs
[14]:./docs/API_reference.md