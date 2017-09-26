#### Node Chaincode Platform

##### ValidateDeploymentSpec
对于node.js的cc来说，必须要有一个`package.json`文件在chaincode的根目录下面，在启动这个chaincode的时候，
会使用`npm install`和`npm start`命令，所以在使用sdk部署chaincode的时候，会对chaincode进行验证，确认是
否有`package.json`文件在根目录下面。

##### GetDeploymentPayload
将chaincodePath目录下除了`node_modules`之外的所有文件打进一个tar包

##### GenerateDockerfile
使用hyperledger/fabric-ccenv构造chaincode的运行image，可以看到在这一步里面是将chaincode复制到
`/usr/local/src`目录下了, 在chaincode部署(instantiate)成功时，可以在`/usr/local/src`看到部署的cc

##### GenerateDockerBuild
build `binpackage.tar`, `binpackage.tar`包含了所有的chaincode源码以及依赖库(node_modules)
`cp -R /chaincode/input/src/* /chaincode/output && cd /chaincode/output && npm install -production`
