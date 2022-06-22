---
stoplight-id: 6enkuv8yb2xa3
---

# Getting Started


![Sifchain Gold Logo.png](https://stoplight.io/api/v1/projects/cHJqOjE0MDgzNw/images/PUu2kq1VfnY)


<p style="text-align: center;">⚡️ T H E &nbsp;&nbsp; O M N I C H A I N &nbsp;&nbsp; J A V A S C R I P T &nbsp;&nbsp; L I B R A R Y ⚡️</p>

<p>&nbsp;</p>

This guide will walk through the basic setup required to create a basic application using **SifchainJS**.

## What is SifchainJS
SifchainJS is a group of Javascript packages that allow developers to interact with Sifchain's network and also other chains within the Cosmos ecosystem via the Inter Blockchain Communication (IBC) protocol. It doesn’t stop there as it also allows for exporting and importing tokens to and from Ethereum via our Peggy bridge.

What we did was to mimic the structure of CosmosJs so as to not reinvent the wheel as some of the packages are dependent on CosmosJs. The packages that make up SifchainJS include:
- **stargate**: Cosmjs stargate client
- **eth-sdk**: Peggy client
- **cosmos-connect**: Cosmos wallet connector
- **math**: Math utilities for sifchain
- **proto-types**: Generated sifnode's rpc typescript definitions
- **common**: Shared utilities, configs and data structures
- **tsconfig**: tsconfig presets
- **e2e**: e2e automation tests 
- **vanir-client**: HTTP client for sifchain Vanir data aggregation service

The main packages include **stargate** and **eth-sdk** with the other packages loaded as dependencies within these two packages.

### Stargate
This package provides a Stargate client that extends **@cosmjs/stargate** with added type definitions and helpers. You can therefore execute any transaction via the various RPC endpoints within the Sifchain network but there are some handy functions that help with simulating various calculations for example when doing a SWAP as well as functions that remove the complexities of some transaction types. The package is available at https://www.npmjs.com/package/@sifchain/stargate and can be installed using **npm i @sifchain/stargate@snapshot**.

### Eth-SDK
This package will allow for interacting with the Peggy bridge so that users can import their ERC-20 tokens from Ethereum into Sifchain and also allow for Exporting these tokens back to Ethereum. To install the Ethereum bridge version of SifchainJS (https://www.npmjs.com/package/@sifchain/eth-sdk) execute the following: **npm i @sifchain/eth-sdk@snapshot**.


## Create a New NPM Project

Create a folder to store your project and within that folder execute the following command to initialize a NPM project:

`npm init`


Answer the questions shown in the terminal and at the end a **package.json** file will be created with a similar structure to the following:

```json
{
  "name": "test-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT"
}
```

## Install Stargate

For this getting started guide we will focus on the standard stargate (Sifnode/IBC) package. To install execute the following command (https://www.npmjs.com/package/@sifchain/stargate):

`npm i @sifchain/stargate@snapshot`


## Get List of Liquidity Pools

Create a new file in the root of the project director “**index.js**”.

Within this file lets import some required components from the Sifchain Stargate package which will allow us to create a query client.

```js
const { createQueryClient } = require("@sifchain/stargate");
```


Let’s now create a function that will pull in all pools available within Sifchain:

```js
async function getPools() {
    const client = await createQueryClient("https://rpc.sifchain.finance");
    const { pools } = await client.clp.getPools({});
    console.log({ pools });
}
```


Let’s now call the function which will print out all the pools:

```js
getPools()
```


The final script should look like the following:

```js
const { createQueryClient } = require("@sifchain/stargate");

async function getPools() {
    const client = await createQueryClient("https://rpc.sifchain.finance");
    const { pools } = await client.clp.getPools({});
    console.log({ pools });
}

getPools();
```


In the terminal execute the following to execute the script:

`node index.js`


This should give a similar output to the following:

```json
{
  pools: [
    {
      externalAsset: [Object],
      nativeAssetBalance: '107876089807750149323482',
      externalAssetBalance: '1295340609723912514933',
      poolUnits: '33061203320698252883208',
      swapPriceNative: '28604602247928070',
      swapPriceExternal: '34959409375110626078',
      rewardPeriodNativeDistributed: '1294447567982139141285'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '147400080850085304622238',
      externalAssetBalance: '18014620058880785055',
      poolUnits: '33813910908762076972371',
      swapPriceNative: '291141767583871',
      swapPriceExternal: '3434752802311938490458',
      rewardPeriodNativeDistributed: '1811549715376424524964'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '22777562751324864355121',
      externalAssetBalance: '73773173546220710591009',
      poolUnits: '1962514538137264717417',
      swapPriceNative: '7715575806676736759',
      swapPriceExternal: '129607954747314314',
      rewardPeriodNativeDistributed: '281682319915476602243'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1136473432574168218478489',
      externalAssetBalance: '6196797288987454019732',
      poolUnits: '168460215500763782761484',
      swapPriceNative: '12989281541392509',
      swapPriceExternal: '76986552083875274958',
      rewardPeriodNativeDistributed: '13134813079550688312660'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '34194149752548124704016',
      externalAssetBalance: '27752269453584023355',
      poolUnits: '7579600109620471338438',
      swapPriceNative: '1933409385454045',
      swapPriceExternal: '517221033315287069124',
      rewardPeriodNativeDistributed: '435666163229433033152'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4128260691724160836675',
      externalAssetBalance: '260631825635846190953',
      poolUnits: '2403731448931735039456',
      swapPriceNative: '150396406654660781',
      swapPriceExternal: '6649095029875529313',
      rewardPeriodNativeDistributed: '48662464271344341430'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '6246436594618504638791',
      externalAssetBalance: '12630791714093391417',
      poolUnits: '1473265987975854416707',
      swapPriceNative: '4816985796652025',
      swapPriceExternal: '207598702198104267926',
      rewardPeriodNativeDistributed: '72439292835842705002'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '82936706754035059033611',
      externalAssetBalance: '1027794972207790455467',
      poolUnits: '35922131578490036458436',
      swapPriceNative: '29521391937152206',
      swapPriceExternal: '33873741527921977381',
      rewardPeriodNativeDistributed: '1047027922036963427772'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '150690305401483283341564',
      externalAssetBalance: '4256180978147897493387',
      poolUnits: '28824261524340026542743',
      swapPriceNative: '67284015406815918',
      swapPriceExternal: '14862371009576168061',
      rewardPeriodNativeDistributed: '1761892046339849325978'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '32212333973788747279996',
      externalAssetBalance: '313447985011094568328',
      poolUnits: '12131857537162414828396',
      swapPriceNative: '23180372861836756',
      swapPriceExternal: '43139944554717088156',
      rewardPeriodNativeDistributed: '386705863663767708293'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '24240471139759852513109',
      externalAssetBalance: '125428372763676839619',
      poolUnits: '13917412366973192348197',
      swapPriceNative: '12326275199930712',
      swapPriceExternal: '81127508819769696753',
      rewardPeriodNativeDistributed: '292500737420779100578'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '18041582342530088809600',
      externalAssetBalance: '3152149263036644417055',
      poolUnits: '322996921372154172640',
      swapPriceNative: '416206901898923954',
      swapPriceExternal: '2402651170463228505',
      rewardPeriodNativeDistributed: '216369555345152024928'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '10653299455134276728188',
      externalAssetBalance: '250457466308124853883',
      poolUnits: '70949563193469290430',
      swapPriceNative: '56005022523136569',
      swapPriceExternal: '17855541431008364836',
      rewardPeriodNativeDistributed: '124861111828532876352'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '15986422832174998468832',
      externalAssetBalance: '3126227848839645717',
      poolUnits: '231611085471163836435',
      swapPriceNative: '465850386596460',
      swapPriceExternal: '2146611936069754147786',
      rewardPeriodNativeDistributed: '187135230410426268259'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '6739214578388773695173',
      externalAssetBalance: '113955470833858047237630',
      poolUnits: '6635008537777666355100',
      swapPriceNative: '40281257828587416324',
      swapPriceExternal: '24825441257520931',
      rewardPeriodNativeDistributed: '82249853649558140287'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5823497507515938697703',
      externalAssetBalance: '4043660397384829715',
      poolUnits: '878667306974432881494',
      swapPriceNative: '1654123412695520',
      swapPriceExternal: '604549812792547446534',
      rewardPeriodNativeDistributed: '69840191527132010305'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4435563820604622032368',
      externalAssetBalance: '28357835',
      poolUnits: '108307208666231059',
      swapPriceNative: '152300513118847',
      swapPriceExternal: '6565965588715686669277',
      rewardPeriodNativeDistributed: '53194944509211899498'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '228082991139438016493363',
      externalAssetBalance: '2078288383430900978487',
      poolUnits: '20844405236831601282612',
      swapPriceNative: '21706511794467323',
      swapPriceExternal: '46069124760032208112',
      rewardPeriodNativeDistributed: '2624134416920120847111'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '53707940715734821089',
      externalAssetBalance: '11740914817120734778350',
      poolUnits: '22720812206069650200',
      swapPriceNative: '520763532787651686016',
      swapPriceExternal: '1920257347306025',
      rewardPeriodNativeDistributed: '644109980518379078'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '16578293985316116853545',
      externalAssetBalance: '7623856909322730327615',
      poolUnits: '16588874359551090145813',
      swapPriceNative: '1095499107227565118',
      swapPriceExternal: '912825937877666263',
      rewardPeriodNativeDistributed: '196733847519153543634'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5688475330883790667885',
      externalAssetBalance: '16962809552362059455504',
      poolUnits: '403283130068616050200',
      swapPriceNative: '7103608779689868545',
      swapPriceExternal: '140773518223461461',
      rewardPeriodNativeDistributed: '73027660706003405630'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5770944939088542393837',
      externalAssetBalance: '4315316442412144699190',
      poolUnits: '4678322628441397660125',
      swapPriceNative: '1781323697872860242',
      swapPriceExternal: '561380282086525737',
      rewardPeriodNativeDistributed: '71130981242774641666'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '14330063360244086489480',
      externalAssetBalance: '5690651673175679792413',
      poolUnits: '5859137536529159677100',
      swapPriceNative: '945999747971069335',
      swapPriceExternal: '1057082734054881541',
      rewardPeriodNativeDistributed: '171857954499630221338'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '693563214807028430858741',
      externalAssetBalance: '10308925539357268907894',
      poolUnits: '154587514460876926236935',
      swapPriceNative: '35408251391874575',
      swapPriceExternal: '28242004636645034470',
      rewardPeriodNativeDistributed: '7987161670577032154354'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '506783171666535341014321',
      externalAssetBalance: '2675730520651216215723',
      poolUnits: '260893238390551198077500',
      swapPriceNative: '12577586579072572',
      swapPriceExternal: '79506508963417350232',
      rewardPeriodNativeDistributed: '5971691600076834337387'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '82010510409840221801',
      externalAssetBalance: '346049425702458474421',
      poolUnits: '49476090091828828720',
      swapPriceNative: '10051844145984652865',
      swapPriceExternal: '99484232492675174',
      rewardPeriodNativeDistributed: '983537770363708380'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '85250247167929079699300047',
      externalAssetBalance: '429745507025344691174',
      poolUnits: '8761235154096896234590873',
      swapPriceNative: '12008614906731',
      swapPriceExternal: '83273548191515148367476',
      rewardPeriodNativeDistributed: '1851343510401821673067911'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '46770006035909536645256840',
      externalAssetBalance: '422646133203659932492556',
      poolUnits: '11618200699400451589856708',
      swapPriceNative: '21527153234215776',
      swapPriceExternal: '46452960550570811604',
      rewardPeriodNativeDistributed: '537855363452548160660892'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '156908879271182620494608',
      externalAssetBalance: '4415302059146327290463',
      poolUnits: '6209826837681984500115',
      swapPriceNative: '67033211665916375',
      swapPriceExternal: '14917978344700301105',
      rewardPeriodNativeDistributed: '1858763849010544047684'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '64121289162885732721325',
      externalAssetBalance: '153948557252264538872',
      poolUnits: '5840991673213194633717',
      swapPriceNative: '5719400462097769',
      swapPriceExternal: '174843500917316420129',
      rewardPeriodNativeDistributed: '765678708908215187564'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '17092080392778596256557',
      externalAssetBalance: '1399346240647588967723',
      poolUnits: '2753781071436457447139',
      swapPriceNative: '195032679193693111',
      swapPriceExternal: '5127345858851296587',
      rewardPeriodNativeDistributed: '489522999008086025128'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '289422853936947600893759',
      externalAssetBalance: '69753536978666411817312',
      poolUnits: '79206907626375695944',
      swapPriceNative: '574130396706945192',
      swapPriceExternal: '1741764598663337350',
      rewardPeriodNativeDistributed: '3238461840054442883697'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '3970281082069931073001',
      externalAssetBalance: '355825030024728424881',
      poolUnits: '398122410104545022680',
      swapPriceNative: '213497290873298080',
      swapPriceExternal: '4683900183961093832',
      rewardPeriodNativeDistributed: '50007877742621120633'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4237914269706671266252',
      externalAssetBalance: '5897422061046021362265',
      poolUnits: '4826168557094069831095',
      swapPriceNative: '3315027773681359882',
      swapPriceExternal: '301656597853826789',
      rewardPeriodNativeDistributed: '50795583884398970511'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '26778911602006874908347',
      externalAssetBalance: '404001553072011381511',
      poolUnits: '2463690253321349631900',
      swapPriceNative: '35939103647985160',
      swapPriceExternal: '27824845321614065294',
      rewardPeriodNativeDistributed: '317958143324885659299'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4474228582985018266860',
      externalAssetBalance: '185366510307007240',
      poolUnits: '1162919155080827534134',
      swapPriceNative: '98693881855823',
      swapPriceExternal: '10132340260075424856311',
      rewardPeriodNativeDistributed: '53201673229291154092'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5196749749503132672029077',
      externalAssetBalance: '92086989640598927492083422',
      poolUnits: '4132630888004742714904100',
      swapPriceNative: '42212745594523633328',
      swapPriceExternal: '23689527556578633',
      rewardPeriodNativeDistributed: '62378396367994807164994'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '2308430549001210115276890',
      externalAssetBalance: '2597272881103063203736436',
      poolUnits: '5452665313449039327900',
      swapPriceNative: '2680265993545503547',
      swapPriceExternal: '373097297957869559',
      rewardPeriodNativeDistributed: '27553110576408598347135'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '415770149362201863962390',
      externalAssetBalance: '569714356348765446635',
      poolUnits: '71915592979758650865567',
      swapPriceNative: '3264231761880441',
      swapPriceExternal: '306350796491123740689',
      rewardPeriodNativeDistributed: '5223941815739992101398'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '3521773773785054454177',
      externalAssetBalance: '61603433904452434168',
      poolUnits: '26013377358492374662',
      swapPriceNative: '41669721184098292',
      swapPriceExternal: '23998240727298460044',
      rewardPeriodNativeDistributed: '44516291096774347602'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '61124538589105290366014',
      externalAssetBalance: '1455609105495666938795',
      poolUnits: '339764770595445371278',
      swapPriceNative: '56729152835512871',
      swapPriceExternal: '17627620897279163843',
      rewardPeriodNativeDistributed: '705374349338035973434'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '947163227217859609166181',
      externalAssetBalance: '9190631129035035498698',
      poolUnits: '87490802494334780900247',
      swapPriceNative: '23115198936030474',
      swapPriceExternal: '43261578791174333527',
      rewardPeriodNativeDistributed: '11242716002672266197865'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '193559339361095119597873',
      externalAssetBalance: '3331490503955999866135',
      poolUnits: '51821152036907998661180',
      swapPriceNative: '41001672913874788',
      swapPriceExternal: '24389248753274110747',
      rewardPeriodNativeDistributed: '2729509331707547141226'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '2834472736155493900033439',
      externalAssetBalance: '1253453298068210755358',
      poolUnits: '888235552359045218943016',
      swapPriceNative: '1053447754067386',
      swapPriceExternal: '949263972567869130894',
      rewardPeriodNativeDistributed: '33713478477620216726751'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '33352856482132975382924',
      externalAssetBalance: '1999005131684121046570',
      poolUnits: '8706106384073578531626',
      swapPriceNative: '142776917852401458',
      swapPriceExternal: '7003933234056446295',
      rewardPeriodNativeDistributed: '399994999857521391271'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5503490891732720116396',
      externalAssetBalance: '311930428746060822330',
      poolUnits: '1992931161687916002',
      swapPriceNative: '135019538221713967',
      swapPriceExternal: '7406335506515864077',
      rewardPeriodNativeDistributed: '64299514157893542350'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '134319776684544282215298',
      externalAssetBalance: '176324068314802888765575',
      poolUnits: '49222948936394766383280',
      swapPriceNative: '3127150369627340748',
      swapPriceExternal: '319779953568245727',
      rewardPeriodNativeDistributed: '1610873691870383492749'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '735405280787906682831053',
      externalAssetBalance: '99158671738346165902793',
      poolUnits: '429941854335101364319300',
      swapPriceNative: '321204103364874585',
      swapPriceExternal: '3113285258566238720',
      rewardPeriodNativeDistributed: '8818168876023058921355'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '228709732658564301450067',
      externalAssetBalance: '3801635017113749422205',
      poolUnits: '89646199367599507614760',
      swapPriceNative: '39597056318882165',
      swapPriceExternal: '25254402547846497303',
      rewardPeriodNativeDistributed: '2970794979676395752415'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4294886000512141558272',
      externalAssetBalance: '7275884611346443567137',
      poolUnits: '2371435820575838167200',
      swapPriceNative: '4035629521814795877',
      swapPriceExternal: '247792815122047862',
      rewardPeriodNativeDistributed: '50761360619322928029'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '11684066772555915439174',
      externalAssetBalance: '2408268373555249096',
      poolUnits: '4025483226394810965782',
      swapPriceNative: '491007336289540',
      swapPriceExternal: '2036629450912695981456',
      rewardPeriodNativeDistributed: '135706255004716995421'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '9865129484896835792811',
      externalAssetBalance: '92287829500902188662',
      poolUnits: '885444185818599400408',
      swapPriceNative: '22285314647472451',
      swapPriceExternal: '44872599549568907693',
      rewardPeriodNativeDistributed: '118310780038273535110'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1506411541555341057986184',
      externalAssetBalance: '1468873843642354677416',
      poolUnits: '442198949143965818491201',
      swapPriceNative: '2322833020272049',
      swapPriceExternal: '430508775761760132537',
      rewardPeriodNativeDistributed: '17932032866740412817431'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '201756172577398428333906',
      externalAssetBalance: '550701466277646070120492',
      poolUnits: '94565521225339582930700',
      swapPriceNative: '6502292927877637060',
      swapPriceExternal: '153791902501335169',
      rewardPeriodNativeDistributed: '2407422553470686483525'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '308289379264377580250582',
      externalAssetBalance: '338522653740540457725101',
      poolUnits: '6168947615499852054800',
      swapPriceNative: '2615810638098231496',
      swapPriceExternal: '382290669452853666',
      rewardPeriodNativeDistributed: '3555596169953994912046'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '19838066488773302762854',
      externalAssetBalance: '5591981742122469577583',
      poolUnits: '127938512280002528640',
      swapPriceNative: '671496156947951688',
      swapPriceExternal: '1489211799133040729',
      rewardPeriodNativeDistributed: '232811682050123241286'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '153032689075519173265924',
      externalAssetBalance: '2003281082318243368685',
      poolUnits: '176610251459916074731',
      swapPriceNative: '31184214450325904',
      swapPriceExternal: '32067506513193679398',
      rewardPeriodNativeDistributed: '1822410172035584889294'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '745353275221901949241113',
      externalAssetBalance: '2700300972525383416869',
      poolUnits: '141919874086704358941089',
      swapPriceNative: '8630324726152457',
      swapPriceExternal: '115870495222834631776',
      rewardPeriodNativeDistributed: '8897330437008458761887'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '254964081176588961139983',
      externalAssetBalance: '167278917367057359284519',
      poolUnits: '45811094671632261179415',
      swapPriceNative: '1562929307783097961',
      swapPriceExternal: '639824203833209123',
      rewardPeriodNativeDistributed: '3040657391954891432403'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '94901231557337904541471',
      externalAssetBalance: '666183422464772833227',
      poolUnits: '1111923606476584258302',
      swapPriceNative: '16722419007599169',
      swapPriceExternal: '59799960734895418670',
      rewardPeriodNativeDistributed: '1847166279766069550488'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '193688287323359960435068',
      externalAssetBalance: '192945143465971782043558881',
      poolUnits: '11914850399365345868909',
      swapPriceNative: '2373053999905411806591',
      swapPriceExternal: '421397911575709',
      rewardPeriodNativeDistributed: '2281764963523016852229'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '3006079982221144053821',
      externalAssetBalance: '16108783115744116483',
      poolUnits: '403269413448671741078',
      swapPriceNative: '12765544120982942',
      swapPriceExternal: '78335869633098953520',
      rewardPeriodNativeDistributed: '37053630848196558548'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '48903911527256925935925',
      externalAssetBalance: '572628775',
      poolUnits: '8792592849995991673770',
      swapPriceNative: '27893736819144555',
      swapPriceExternal: '35850341708338430486',
      rewardPeriodNativeDistributed: '582774443178344116950'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '3720943907424255400528',
      externalAssetBalance: '65252492188979623148',
      poolUnits: '201991354909358760199',
      swapPriceNative: '41775447274325426',
      swapPriceExternal: '23937505527088320997',
      rewardPeriodNativeDistributed: '42364985037522123167'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '19808272037965644984108',
      externalAssetBalance: '139036844704123802864',
      poolUnits: '7534029579293919316194',
      swapPriceNative: '16720930425041830',
      swapPriceExternal: '59805284430454569869',
      rewardPeriodNativeDistributed: '269486236455479971699'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '11759597856431172069946',
      externalAssetBalance: '397598067769039224206',
      poolUnits: '1750945676721580132220',
      swapPriceNative: '80543207927934782',
      swapPriceExternal: '12415696192300389332',
      rewardPeriodNativeDistributed: '150304565395042304998'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '72068497266329818442548',
      externalAssetBalance: '1658448843540428544496795',
      poolUnits: '76239683782113678340000',
      swapPriceNative: '54819332306800780306',
      swapPriceExternal: '18241739874029849',
      rewardPeriodNativeDistributed: '876581155592718900032'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '17635183162820646585840',
      externalAssetBalance: '60320355684748863117',
      poolUnits: '6828661928190410283569',
      swapPriceNative: '8148188122602847',
      swapPriceExternal: '122726670645349682471',
      rewardPeriodNativeDistributed: '221223038093202481889'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '917348785995987172993',
      externalAssetBalance: '224148018993253188693508',
      poolUnits: '21751349480186089210',
      swapPriceNative: '582073117974042331203',
      swapPriceExternal: '1717997222503030',
      rewardPeriodNativeDistributed: '11001604247010361138'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5251278342444853636232',
      externalAssetBalance: '76149145760057793431',
      poolUnits: '8136049464814478330',
      swapPriceNative: '34544357973404840',
      swapPriceExternal: '28948287323745428565',
      rewardPeriodNativeDistributed: '57796877340945528008'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '862878655153478927043925',
      externalAssetBalance: '4176091144756591660430353516',
      poolUnits: '177710662456077496045509',
      swapPriceNative: '11529152157875708445381',
      swapPriceExternal: '86736647003562',
      rewardPeriodNativeDistributed: '10333417202611868763719'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '2160892283797678846502',
      externalAssetBalance: '13403130180171373258',
      poolUnits: '567032750443345446383',
      swapPriceNative: '14775774187225719',
      swapPriceExternal: '67678348855565122685',
      rewardPeriodNativeDistributed: '25479978910190212930'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '110074017988720961292376',
      externalAssetBalance: '248641584810622988836',
      poolUnits: '38411472011986986854055',
      swapPriceNative: '5381038185070566',
      swapPriceExternal: '185837744640112305697',
      rewardPeriodNativeDistributed: '1344993385588658178630'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '150895232205659038681928378',
      externalAssetBalance: '1361027797781',
      poolUnits: '17931864513545152257142687',
      swapPriceNative: '21486650009573757',
      swapPriceExternal: '46540526304844286368',
      rewardPeriodNativeDistributed: '3184548881411090473189425'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '2790289855254715165927989',
      externalAssetBalance: '25216115035',
      poolUnits: '262107603012591341102009',
      swapPriceNative: '21528114032322192',
      swapPriceExternal: '46450887358844141893',
      rewardPeriodNativeDistributed: '32071188017807483323365'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '62032077290824755999137',
      externalAssetBalance: '16094609792585403328581',
      poolUnits: '6827640798051895083475',
      swapPriceNative: '618074922471491037',
      swapPriceExternal: '1617926829970291501',
      rewardPeriodNativeDistributed: '0'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '981125491496856462720588',
      externalAssetBalance: '30878293',
      poolUnits: '98084293857882325944301',
      swapPriceNative: '749731017034',
      swapPriceExternal: '1333810461607282050395740',
      rewardPeriodNativeDistributed: '14087966851244461557127'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '155217257723247422454',
      externalAssetBalance: '433428407855593088',
      poolUnits: '10704420732273598225',
      swapPriceNative: '6652034521446863',
      swapPriceExternal: '150329947423924105756',
      rewardPeriodNativeDistributed: '1861493542963934257'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '109573963633374674803585',
      externalAssetBalance: '695856942',
      poolUnits: '3466681345058170296792',
      swapPriceNative: '15128285806186132',
      swapPriceExternal: '66101342206596604880',
      rewardPeriodNativeDistributed: '1222584591977125818567'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5033404727583158416079',
      externalAssetBalance: '8919531293766427',
      poolUnits: '165393711190044010704',
      swapPriceNative: '4221408489034',
      swapPriceExternal: '236887797981458638429334',
      rewardPeriodNativeDistributed: '69825822950841744829'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '2500002730232065858196685',
      externalAssetBalance: '1335683899616582',
      poolUnits: '628106016547215000598971',
      swapPriceNative: '127274188386013698',
      swapPriceExternal: '7857052656788444457',
      rewardPeriodNativeDistributed: '28978810092973042549176'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '6689413311504441799754544',
      externalAssetBalance: '91311105696409942245302',
      poolUnits: '4456840940974611040918945',
      swapPriceNative: '32517166883935059',
      swapPriceExternal: '30752986677762644024',
      rewardPeriodNativeDistributed: '77699858245411460211345'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '60569388649329253290210',
      externalAssetBalance: '1968338991089087969005',
      poolUnits: '15120217109827709989710',
      swapPriceNative: '77414771028867843',
      swapPriceExternal: '12917431476036149954',
      rewardPeriodNativeDistributed: '726398130760919561457'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '533089396422478242813482',
      externalAssetBalance: '214254622098',
      poolUnits: '158682260911012437608100',
      swapPriceNative: '957430556188943499',
      swapPriceExternal: '1044462173812294898',
      rewardPeriodNativeDistributed: '6245152190706505867359'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '519124018003810296708640',
      externalAssetBalance: '168996072229',
      poolUnits: '177634050432991667837231',
      swapPriceNative: '775501218177406532',
      swapPriceExternal: '1289488625610768675',
      rewardPeriodNativeDistributed: '0'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1261237866368344884768328',
      externalAssetBalance: '9931544275836',
      poolUnits: '27907069520012400457683700',
      swapPriceNative: '18758448276854168155',
      swapPriceExternal: '53309313501892451',
      rewardPeriodNativeDistributed: '14724840838050464781391'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '308744639321143871391209275',
      externalAssetBalance: '289232016822',
      poolUnits: '27448835097219360615118893',
      swapPriceNative: '2231640181483446',
      swapPriceExternal: '448100911866636603777',
      rewardPeriodNativeDistributed: '6625578915939876073466308'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '7481954237853213545807547',
      externalAssetBalance: '229649263710',
      poolUnits: '3027622420846732182375010',
      swapPriceNative: '73118477198131463',
      swapPriceExternal: '13676433622508531630',
      rewardPeriodNativeDistributed: '87276011124098723833330'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '195925190408581445072543',
      externalAssetBalance: '999531987',
      poolUnits: '36001339453971992458098',
      swapPriceNative: '12153004301266647',
      swapPriceExternal: '82284180378701113871',
      rewardPeriodNativeDistributed: '1956933328454700902331'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '542240291171654773008',
      externalAssetBalance: '95064858449078059',
      poolUnits: '40228865248486053220',
      swapPriceNative: '417643137218951876456075863',
      swapPriceExternal: '2394388680',
      rewardPeriodNativeDistributed: '9215497104694851650'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '8513256254646488897217926',
      externalAssetBalance: '178913729164',
      poolUnits: '1706695664426007535916622',
      swapPriceNative: '50063947462788735',
      swapPriceExternal: '19974453687112212604',
      rewardPeriodNativeDistributed: '99371977220861612261176'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '5918864963660231040767537',
      externalAssetBalance: '52612120214',
      poolUnits: '1387049812454411631253344',
      swapPriceNative: '21175052712732639',
      swapPriceExternal: '47225384204367270162',
      rewardPeriodNativeDistributed: '68075583853241851926259'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '15801959614228019321',
      externalAssetBalance: '130697',
      poolUnits: '10042234224948775122',
      swapPriceNative: '19702974700361199',
      swapPriceExternal: '50752980848775025903',
      rewardPeriodNativeDistributed: '27132574168072028'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '4495166908927275137910137',
      externalAssetBalance: '51835961744',
      poolUnits: '1115681820466321187024111',
      swapPriceNative: '27470240828622879',
      swapPriceExternal: '36403029962176546825',
      rewardPeriodNativeDistributed: '52509517163740095580715'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1710370571257098546073678',
      externalAssetBalance: '9095503009487',
      poolUnits: '519474471263960725500629',
      swapPriceNative: '126681627647369004',
      swapPriceExternal: '7893804481200221831',
      rewardPeriodNativeDistributed: '20020886154891354624488'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '3752764090873392596012132',
      externalAssetBalance: '24075892745',
      poolUnits: '117935407439059766039987',
      swapPriceNative: '15282987708302828',
      swapPriceExternal: '65432232162831649719',
      rewardPeriodNativeDistributed: '46612281142466843564048'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1122804366451844835489366',
      externalAssetBalance: '195376244348',
      poolUnits: '1789055541595703811272100',
      swapPriceNative: '414519334650783102',
      swapPriceExternal: '2412432705533241682',
      rewardPeriodNativeDistributed: '13264018943305409867788'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1742563839830262637999159',
      externalAssetBalance: '11410078562',
      poolUnits: '785391704948679046333549',
      swapPriceNative: '15598292688886820',
      swapPriceExternal: '64109580442170086047',
      rewardPeriodNativeDistributed: '20297675863403042297210'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '1466047926615732461748979',
      externalAssetBalance: '96593383101339',
      poolUnits: '4163830513005949882444970',
      swapPriceNative: '156955379398159498501',
      swapPriceExternal: '6371237506063539',
      rewardPeriodNativeDistributed: '0'
    },
    {
      externalAsset: [Object],
      nativeAssetBalance: '10378346772734114164568',
      externalAssetBalance: '2981253274',
      poolUnits: '3610321548650560672710',
      swapPriceNative: '684302022882411465',
      swapPriceExternal: '1461343041362407067',
      rewardPeriodNativeDistributed: '112779909748498863686'
    },
    ... 1 more item
  ]
}
```

https://stackblitz.com/edit/sifchain-pools-demo?embed=1&file=index.tsx

