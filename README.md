# Voyager — Azure Bot

An early conversational companion for the [Voyager travel website](https://github.com/Cyberfaour/Voyager-Traveling-System). The project uses Bot Framework Composer dialogs and a question-and-answer knowledge base to help visitors with website navigation, registration, and general questions.

**Status:** historical project source. The legacy cloud recognizers require migration for a new working deployment. Source inspection and the build commands below do not establish that the original hosted bot remains available.

[Ali Faour's portfolio](https://cyberfaour.github.io/Portfolio/)

## What the source demonstrates

- An adaptive dialog with greeting, unknown-intent, and question-answer matching handlers.
- A knowledge-base source containing Voyager-specific questions, registration help, and conversational responses.
- Language-generation templates for bot replies and recognizer definitions for LUIS and QnA Maker.
- An ASP.NET Core host that routes bot activities to the configured Bot Framework adapter.
- Composer project assets that separate conversation content from the host application.

The host was generated from Composer's Empty Bot template. The dialog and knowledge-base files are the most useful starting points for reviewing the Voyager-specific behavior; the generated C# host provides the runtime integration.

## Technology

| Layer | Checked-in implementation |
| --- | --- |
| Host | C#, ASP.NET Core; target framework `netcoreapp3.1` |
| Bot runtime | Bot Framework Adaptive Runtime 4.17.1 |
| Conversation authoring | Bot Framework Composer `.botproj`, `.dialog`, `.lg`, `.lu`, and `.qna` files |
| Legacy recognizers | LUIS and QnA Maker packages, version 4.17.1 |

See [VoyagerBotNew.csproj](VoyagerBotNew/VoyagerBotNew.csproj) for package versions.

## Start with these files

| Location | Purpose |
| --- | --- |
| [VoyagerBotNew.botproj](VoyagerBotNew/VoyagerBotNew.botproj) | Composer project entry point |
| [VoyagerBotNew.dialog](VoyagerBotNew/VoyagerBotNew.dialog) | Main conversation and event handling |
| [Knowledge-base source](VoyagerBotNew/knowledge-base/source/VoyagerBotKB.source.en-us.qna) | Source question-and-answer content |
| [language-generation](VoyagerBotNew/language-generation) | Reply templates |
| [recognizers](VoyagerBotNew/recognizers) | Language and question-answer recognizer definitions |
| [Program.cs](VoyagerBotNew/Program.cs) and [Startup.cs](VoyagerBotNew/Startup.cs) | Host configuration and runtime registration |
| [BotController.cs](VoyagerBotNew/Controllers/BotController.cs) | Activity routing through `api/{route}` |

## Inspect or build the original host

The source files can be reviewed directly on GitHub. A compatible Bot Framework Composer installation can open `VoyagerBotNew/VoyagerBotNew.botproj` to inspect the conversation visually.

Building the historical host requires a compatible .NET Core 3.1 SDK environment and access to its NuGet dependencies. From the repository root:

```sh
dotnet restore VoyagerBotNew/VoyagerBotNew.csproj
dotnet build VoyagerBotNew/VoyagerBotNew.csproj --no-restore
```

These commands have not been run as part of this documentation update. Review the original project and dependencies in an isolated development environment before running the host.

## Runtime configuration and migration

The recognizers refer to these configuration paths; values must come from resources and credentials you control:

| Configuration path | Original purpose |
| --- | --- |
| `qna:hostname`, `qna:endpointKey` | QnA Maker endpoint and authentication |
| `qna:VoyagerBotNew_en_us_qna` | Generated knowledge-base reference |
| `luis:endpoint`, `luis:endpointKey` | LUIS prediction endpoint and authentication |
| `luis:VoyagerBotNew_en_us_lu:appId`, `luis:VoyagerBotNew_en_us_lu:version` | Language model selection |
| `MicrosoftAppId` | Bot application identity |

Keep any replacement credentials outside version control. The checked-in configuration records the historical project structure and should not be reused as a deployment configuration.

Microsoft's [Azure Language service status](https://azure.microsoft.com/en-us/products/ai-foundry/tools/language#faq) states that QnA Maker was fully retired on 31 October 2025 and LUIS on 31 March 2026. Restoring the host alone cannot restore those services. A working successor needs updated recognizers and service integration, with Custom Question Answering and Conversational Language Understanding as Microsoft's migration destinations.

After adapting the runtime and configuring working services, the original host launch command is:

```sh
dotnet run --project VoyagerBotNew/VoyagerBotNew.csproj --launch-profile BotProject
```

The checked-in `BotProject` profile listens at `http://localhost:3978`. Activity paths are handled by `BotController` under `api/{route}`, using the enabled adapter's route.

The nested [template README](VoyagerBotNew/README.md) and deployment scripts preserve the original Composer workflow. Their provisioning instructions need reassessment against current services before reuse; this repository's documentation refresh does not deploy resources or claim a current end-to-end test.
