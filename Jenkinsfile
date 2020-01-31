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
            docker_host="www.j116.cn"
            docker_img_name="${docker_host}/sso/boot1"
            tag_name="prod-${BUILD_ID}"
            sh "docker build -t ${docker_img_name}:${tag_name} . "
            withCredentials([usernamePassword(credentialsId: 'harbor', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
            sh "docker login -u ${dockerUser} -p ${dockerPassword}  ${docker_host}"
            sh "docker push ${docker_img_name}:${tag_name}"
        }    
       }
	}
	stage('运行镜像') {
         dir('./') {
         sh '''
            ssh c32 mkdir -p /data/k8s2
            scp -rp k8s/* c32:/data/k8s2/
            ssh  c32  sed -i "s|www.j116.cn/sso/boot1:prod-xxx|www.j116.cn/sso/boot1:${tag_name}|g" /data/k8s2/deployment.yaml
            ssh  c32  sudo kubectl apply -f  /data/k8s2/deployment.yaml
        '''   
    }
	}
}
