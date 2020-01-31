node{
	stage('拉取代码') {
		checkout scm
	}
	stage('maven编译') {
		dir('./') {
		sh '/usr/local/maven3/bin/mvn  clean install -DskipTests=true'
       }
	}
	stage('镜像构建') {
		dir('./') {
            docker_img_name='www.j116.cn/sso/boot1'
           withCredentials([usernamePassword(credentialsId: 'docker-register', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
            sh "docker login -u ${dockerUser} -p ${dockerPassword}  www.j116.cn"
            sh "docker push ${docker_img_name}:prod-${BUILD_ID}"
        }    
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
