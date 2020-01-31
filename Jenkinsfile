node{
	stage('拉取代码') {
		checkout scm
	}
	stage('maven编译') {
		dir('./') {
		sh 'mvn clean install -DskipTests=true'
       }
	}
	stage('镜像构建') {
		dir('./') {
        docker.build("www.j116.cn/sso/boot1:prod-${BUILD_ID}").push()
       }
	}
	stage('运行镜像') {
		dir('./') {
        sh '''
            scp -rp k8s/* huangyan@c32:/data/k8s/
            ssh  huangyan@c32  sudo kubectl apply  /data/k8s/*
        '''   
    }
	}
}
