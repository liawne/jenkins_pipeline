/* VERSION 1.5 */
properties([

    /* Job throttle config. */
    [$class: 'ThrottleJobProperty', 
        categories: [], 
        limitOneJobWithMatchingParams: false, 
        maxConcurrentPerNode: 0, 
        maxConcurrentTotal: 0, 
        paramsToUseForLimit: '', 
        throttleEnabled: false, 
        throttleOption: 'project'], 

    /* Only keep the 10 most recent builds. */
    [$class: 'BuildDiscarderProperty',
        strategy: 
            [$class: 'LogRotator', 
            numToKeepStr: '10']],

    parameters([

        /* Private cloud environment selection. */
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '选择测试版本', 
            filterLength: 1, 
            filterable: false, 
            name: 'version', 
            randomName: 'choice-parameter-14056768582585236', 
            script: 
                [$class: 'GroovyScript', 
                    fallbackScript: 
                    [classpath: [], 
                    sandbox: false, 
                script: 
                    'return ["error"]'], 
                script: 
                    [classpath: [], sandbox: false, script: '''def html = "http://xx.xx.xx.xx/ipaddr/test_version".toURL().text
def list=html.readLines()
Collection<String> coll = new ArrayList<String>();

list.each{ value ->
 coll.add(value)
}
return coll''']]], 

        /* Function the work selection. */
        choice(
            choices: ['完整功能', '部分功能'], 
            description: '完整功能为默认选项,为执行CI的所有步骤；部分功能可供选择,表示用户手动选择需要执行的CI步骤', 
            name: 'func_select'), 

        /* Choose sections to execute. */
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_MULTI_SELECT', 
            description: '当func_select参数选择了部分功能，此处为选择需要执行的内容;查看文参数说明: http://xxxxxxxxxxxx', 
            filterLength: 1, 
            filterable: false, 
            name: 'func_list', 
            randomName: 'choice-parameter-7084527143345940', 
            referencedParameters: 'func_select', 
            script: 
            [$class: 'GroovyScript', 
                fallbackScript: 
                    [classpath: [], 
                    sandbox: false, 
                    script: ''], 
                script: [classpath: [], 
                    sandbox: false, 
                    script: '''if (func_select.contains("部分功能")){
  return ["pass_vars", "env_extract", "rpm_packet", "trove_upload", "env_update", "ecapi_varfile_upload", "run_test_instances"]
}else if (func_select.contains("完整功能")){
  return ["all:selected"]
}else{
  return ["Blank:selected"]
}''']]],

        /* Function the work selection. */
        choice(
            choices: ['None', 'public', 'private'], 
            description: '选择打包的类型，private打包私有云带license加壳的包，public打包公有云带license加壳的包，None打的包不含license和代码加壳，适用于公有云和私有云',  
            name: 'rpm_type'),

        /* Input the change id of upgrade code. */
        string(
            description: '此处填写需要测试的组件的代码change id,问开发要相应的changeid,格式为<changeid1>,<changeid2>,...\n示例: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx,xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
            defaultValue: 'None',
            name: 'source_code_rid'),

        /* Input the change id of packet code. */
        string(
            description: '此处填写打包工具代码的change id,问运营要相应的changeid,大部分情况可以忽略,格式为<changeid1>,<changeid2>,...\n示例: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx,xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
            defaultValue: 'None',
            name: 'packet_code_rid'),

        /* Input the change id of playbook code match with the upgrade code. */
        string(
            description: '此处填写需要升级的云环境版本playbook对应的playname', 
            defaultValue: 'ci_xxxxx',
            name: 'playname'), 

        /* The running type to choose */
        choice(
            choices: ['功能测试', '集成测试'], 
            description: '此处选择CI测试的功能,选择功能测试或者集成测试', 
            name: 'test_type'), 
            
        /* Select jobs to run. */
		[$class: 'ChoiceParameter', 
			choiceType: 'PT_CHECKBOX', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'jobs', 
			randomName: 'choice-parameter-4035009811348343', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: 'return["error"]'], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''def html = "http://xx.xx.xx.xx/ipaddr/test_jobs".toURL().text
def list=html.readLines()''']]], 

        /* Select test_compute testing jobs. */
		[$class: 'CascadeChoiceParameter', 
			choiceType: 'PT_MULTI_SELECT', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'cmp_jobs', 
			randomName: 'choice-parameter-4035009815879025', 
			referencedParameters: 'jobs,test_type', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: ''], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''if (jobs.contains("test_compute")){
  def html = "http://xx.xx.xx.xx/ipaddr/test_compute".toURL().text
  def list=html.readLines()
}else{
  return ["Blank:selected"]
}''']]], 

        /* Select test_network testing jobs. */
		[$class: 'CascadeChoiceParameter', 
			choiceType: 'PT_MULTI_SELECT', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'net_jobs', 
			randomName: 'choice-parameter-4035009817916651', 
			referencedParameters: 'jobs,test_type', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: ''], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''if (jobs.contains("test_network")){
  def html = "http://xx.xx.xx.xx/ipaddr/test_network".toURL().text
  def list=html.readLines()
}else{
  return ["Blank:selected"]
}
''']]], 

        /* Select test_storage testing jobs. */
		[$class: 'CascadeChoiceParameter', 
			choiceType: 'PT_MULTI_SELECT', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'stg_jobs', 
			randomName: 'choice-parameter-4035009819722472', 
			referencedParameters: 'jobs,test_type', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: ''], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''if (jobs.contains("test_storage")){
  def html = "http://xx.xx.xx.xx/ipaddr/test_storage".toURL().text
  def list=html.readLines()
}else{
  return ["Blank:selected"]
}
''']]], 

        /* Select test_trove testing jobs. */
		[$class: 'CascadeChoiceParameter', 
			choiceType: 'PT_MULTI_SELECT', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'trove_jobs', 
			randomName: 'choice-parameter-4035009821442792', 
			referencedParameters: 'jobs,test_type', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: ''], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''if (jobs.contains("test_trove")){
  def html = "http://xx.xx.xx.xx/ipaddr/test_trove".toURL().text
  def list=html.readLines()
}else{
  return ["Blank:selected"]
}
''']]], 

        /* Select EC_API_test testing jobs. */
		[$class: 'CascadeChoiceParameter', 
			choiceType: 'PT_MULTI_SELECT', 
			description: '', 
			filterLength: 1, 
			filterable: false, 
			name: 'ec_jobs', 
			randomName: 'choice-parameter-4035009823467846', 
			referencedParameters: 'jobs,test_type', 
			script: 
			[$class: 'GroovyScript', 
				fallbackScript: 
					[classpath: [], 
					sandbox: false, 
					script: ''], 
				script: 
					[classpath: [], 
					sandbox: false, 
					script: '''if (jobs.contains("EC_API_test")){
  def html = "http://xx.xx.xx.xx/ipaddr/EC_API_test".toURL().text
  def list=html.readLines()
}else{
  return ["Blank:selected"]
}
''']]],
    
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '', 
            filterLength: 1, 
            filterable: false, 
            name: 'running_tag', 
            randomName: 'choice-parameter-14056768588024730', 
            referencedParameters: 'version', 
            script: 
                [$class: 'GroovyScript', 
                    fallbackScript: 
                    [classpath: [], 
                    sandbox: false, 
                script: 
                    'return ["无对应的running_tag"]'], 
                script: [classpath: [], sandbox: false, script: '''Collection<String> coll = new ArrayList<String>();
coll.add("level200")
if (version.contains("v")){
      coll.add(version)
}
return coll''']]],

        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '选择测试分支，默认master', 
            filterLength: 1, 
            filterable: false, 
            name: 'test_version', 
            randomName: 'choice-parameter-2497851393086014', 
            script: 
            [$class: 'GroovyScript', 
                fallbackScript: 
                    [classpath: [], 
                    sandbox: false, 
                    script: 'return ["error"]'], 
                script: 
                    [classpath: [], 
                    sandbox: false, 
                    script: '''
def html = "http://xx.xx.xx.xx:8000/BA-U/Version.txt".toURL().text
def list=html.readLines()
Collection<String> coll = new ArrayList<String>();

list.each{ value ->
  if(value.contains("master")){
    coll.add(value+":selected") 
  }else{
    coll.add(value)
  }
}
return coll
''']]]
  ])
])

/ * Set global parameter ipaddr */
def ipaddr
def Url
def userName

/* Add the try for sending email while the build is failed. */
try{

node('xx.xx.xx.xx') {

    /* Get the job build user name */
	wrap([$class: 'BuildUser']) { userName = "${BUILD_USER}" }

    /* Get the ipaddr according to the job name */
    String[] job_ip_maps = "http://xx.xx.xx.xx/ipaddr/job_map".toURL().text.split("\\n");
    def jobName = env.JOB_NAME

    for (String job_ip_map : job_ip_maps) {
      if (job_ip_map.contains("${jobName}")){
        ipaddr = job_ip_map.substring(job_ip_map.lastIndexOf(":") + 1, job_ip_map.length())
      }
    }

    if (env.rpm_type == 'None') {
      rpm_type = ''
    }

    /* Get the Url according to the ipaddr */
    if (ipaddr.contains("xx.xx.xx.xx")){
      Url="http://xx.xx.xx.xx:8081"
    }else if (ipaddr.contains("xx.xx.xx.xx")){
      Url="http://xx.xx.xx.xx:8081"
    }else if (ipaddr.contains("xx.xx.xx.xx")){
      Url="http://xx.xx.xx.xx:8081"
    }else if (ipaddr.contains("xx.xx.xx.xx")){
      Url="http://xx.xx.xx.xx:8081"
    }else{
      return ["None"]
    }


    def remote = [:]
    remote.name = "$ipaddr"
    remote.host = "$ipaddr"
    remote.user = 'root'
    remote.password = 'xxxxxxxxx'
    remote.allowAnyHosts = true

    if (env.test_type == '功能测试') {
      running_type = 'func_test'
    } else if (env.test_type == '集成测试') {
      running_type = 'integrate_test'
    }

    /* The first stage. */
    stage('变量传递') {
        if (env.func_list.contains("all") || env.func_list.contains("pass_vars")) {
            sh "echo export version=${version} > GlobVar"
            sh "echo Url=${Url} >> GlobVar"
            sh "echo ipaddr=${ipaddr} >> GlobVar"
            sh "echo packet_code_rid=${packet_code_rid} >> GlobVar"
            sh "echo source_code_rid=${source_code_rid} >> GlobVar"
			sh "echo export rpm_type=${rpm_type} >> GlobVar"
            sh "echo playname=${playname} >> GlobVar"
            sh "echo trove_jobs=${trove_jobs} >> GlobVar"
            sh "echo ec_jobs=${ec_jobs} >> GlobVar"
            sh "echo export running_type=${running_type} >> GlobVar"
            sh "cat GlobVar ../utils.sh > ciop.sh"
            sh "sed -i '1i\\#!/bin/bash' ciop.sh"
            sshPut remote: remote, from: 'ciop.sh', into: '/tmp/'
            } else {
            echo 'Skip'
        }
    }

    /* The second stage. */
    stage('重构集群环境') {
        if (env.func_list.contains("all") || env.func_list.contains("env_extract")) {
            sshCommand remote: remote, command: 'bash /tmp/ciop.sh env_extract'
            } else {
            echo 'Skip'
        }
    }

    /* The third stage. */
    stage('代码打包') {
        if (env.func_list.contains("all") || env.func_list.contains("rpm_packet")) {
            sshCommand remote: remote, command: 'bash /tmp/ciop.sh rpm_packet'
            } else {
            echo 'Skip'
        }
    }

    /* The fourth stage. */
    stage('trove镜像导入') {
        if (env.func_list.contains("all") || env.func_list.contains("trove_upload")) {
            sshCommand remote: remote, command: 'bash /tmp/ciop.sh trove_upload'
            } else {
            echo 'Skip'
            }
        }

    /* The fifth stage. */
    stage('环境升级') {
        if (env.func_list.contains("all") || env.func_list.contains("env_update")) {
            sshCommand remote: remote, command: 'bash /tmp/ciop.sh env_update'
            } else {
            echo 'Skip'
            }
        }

    /* The sixth stage. */
    stage('文件上传') {
        if (env.func_list.contains("all") || env.func_list.contains("ecapi_varfile_upload")) {
            sshCommand remote: remote, command: 'bash /tmp/ciop.sh ecapi_varfile_upload'
            } else {
            echo 'Skip'
            }
        }
    }
}

catch(exi){
    node('xx.xx.xx.xx') {
        stage('构建失败') {
            def mailRecipients = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
            def jobName = currentBuild.fullDisplayName
            Date date = new Date()
			String datePart = date.format("yyyy/MM/dd")
    
            emailext attachLog: true, body: "job构建失败,在环境准备环节出现错误,测试用例尚未运行,请检查构建日志,排查具体错误原因.\n构建URL:   ${env.BUILD_URL}\n构建序号:  ${env.BUILD_NUMBER}\n任务名称:  ${env.JOB_NAME}\njenkins server帐号密码:  请使用自己分配的账号密码登录\n构建区间:  ${env.WORKSPACE}\n构建触发:  由用户${userName}触发\n控制台:  ${Url}\n控制台帐号密码:  xxxxx/xxxxxxx\nmonitor IP:  ${ipaddr}\nmonitor帐号密码:  xxxxxx/xxxxxxx", 
                replyTo: "${mailRecipients}", 
                subject: "[Jenkins] ${datePart} ${version} ${test_type} 每日构建${jobName}构建失败", 
                to: "${mailRecipients}"

			currentBuild.rawBuild.result = Result.ABORTED
			throw new hudson.AbortException('Job Aborted !')
        }   
    }   

}

/* The test stage. */
stage('执行测试用例'){
  if (env.func_list.contains("all") || env.func_list.contains("run_test_instances")) {
    def running_type
    
    if (env.test_type == '功能测试') {
      running_type = 'func_test'
    } else if (env.test_type == '集成测试') {
      running_type = 'integrate_test'
    }
    
    def tasks = [:]
    
    def test_trove_url
    def test_compute_url
    def test_storage_url
    def test_network_url
    def test_ecs_url
    
    def test_trove_status
    def test_compute_status
    def test_storage_status
    def test_network_status
    def test_ecs_status

    tasks["test_trove"] = {
    	node('win7-CI') {
    	  stage('test_trove') {
    	    if (env.trove_jobs == 'Blank') {
    	      echo 'No test_trove jobs, skip'
    	    } else {
            def handle = triggerRemoteJob(
    	      remoteJenkinsName: 'LocalJenkins',
    	      job: 'test_trove',
    	      parameters: "Url=$Url\nVersion=$test_version\nstage=$running_type\nrunning_tag=$running_tag\nmodule=$trove_jobs",
    	      blockBuildUntilComplete: true,
              shouldNotFailBuild: true,
              useCrumbCache: true,
              useJobInfoCache: true
    	      )
    	  	echo 'Remote Status: ' + handle.getBuildStatus().toString()
            test_trove_url = handle.getBuildUrl().toString()
            test_trove_status = handle.getBuildStatus().toString()
    	    }
    	  }
    	}
    }
    tasks["test_compute"] = {
    	node('win7-CI') {
    	  stage('test_compute') {
    	    if (env.cmp_jobs == 'Blank') {
    	      echo 'No test_compute jobs, skip'
    	    } else {
            def handle = triggerRemoteJob(
    	  	  remoteJenkinsName: 'LocalJenkins',
    	  	  job: 'test_compute',
    	  	  parameters: "Url=$Url\nVersion=$test_version\nstage=$running_type\nrunning_tag=$running_tag\nmodule=$cmp_jobs",
    	  	  blockBuildUntilComplete: true,
              shouldNotFailBuild: true,
              useCrumbCache: true,
              useJobInfoCache: true
    	  	  )
    	  	echo 'Remote Status: ' + handle.getBuildStatus().toString()
            test_compute_url = handle.getBuildUrl().toString()
            test_compute_status = handle.getBuildStatus().toString()
    	    }
    	  }
    	}
    }
    tasks["test_storage"] = {
    	node('win7-CI') {
    	  stage('test_storage') {
    	    if (env.stg_jobs == 'Blank') {
    	      echo 'No test_storage jobs, skip'
    	    } else {
            def handle = triggerRemoteJob(
    	      remoteJenkinsName: 'LocalJenkins',
    	      job: 'test_storage',
    	      parameters: "Url=$Url\nVersion=$test_version\nstage=$running_type\nrunning_tag=$running_tag\nmodule=$stg_jobs",
    	      blockBuildUntilComplete: true,
              shouldNotFailBuild: true,
              useCrumbCache: true,
              useJobInfoCache: true
    	      )
    	  	echo 'Remote Status: ' + handle.getBuildStatus().toString()
            test_storage_url = handle.getBuildUrl().toString()
            test_storage_status = handle.getBuildStatus().toString()
    	    }
    	  }
    	}
    }
    tasks["test_network"] = {
    	node('win7-CI') {
    	  stage('test_network') {
    	    if (env.net_jobs == 'Blank') {
    	      echo 'No test_network jobs, skip'
    	    } else {
            def handle = triggerRemoteJob(
    	      remoteJenkinsName: 'LocalJenkins',
    	      job: 'test_network',
    	      parameters: "Url=$Url\nVersion=$test_version\nstage=$running_type\nrunning_tag=$running_tag\nmodule=$net_jobs",
    	      blockBuildUntilComplete: true,
              shouldNotFailBuild: true,
              useCrumbCache: true,
              useJobInfoCache: true
    	      )
    	  	echo 'Remote Status: ' + handle.getBuildStatus().toString()
            test_network_url = handle.getBuildUrl().toString()
            test_network_status = handle.getBuildStatus().toString()
    	    }
    	  }
    	}
    }
    tasks["EC_API_test"] = {
    	node('win7-CI') {
    	  stage('EC_API_test') {
    	    if (env.ec_jobs == 'Blank') {
    	      echo 'No EC_API_test jobs, skip'
    	    } else {
            def handle = triggerRemoteJob(
    	  	  remoteJenkinsName: 'LocalJenkins',
    	  	  job: 'EC_API_test',
    	  	  parameters: "Url=$Url\nVersion=$test_version\nstage=$running_type\nmodule=$ec_jobs\nenv_version=$version\nipaddr=$ipaddr",
    	  	  blockBuildUntilComplete: true,
              shouldNotFailBuild: true,
              useCrumbCache: true,
              useJobInfoCache: true
    	  	  )
    	  	echo 'Remote Status: ' + handle.getBuildStatus().toString()
            test_ecs_url = handle.getBuildUrl().toString()
            test_ecs_status = handle.getBuildStatus().toString()
    	    }
    	  }
    	}
    }
    
    
    try{
      parallel tasks
    }
    
    finally{
      node('xx.xx.xx.xx') {
        stage('邮件发送') {
          def mailRecipients = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
          def jobName = currentBuild.fullDisplayName
          Date date = new Date()
		  String datePart = date.format("yyyy/MM/dd")
      
          emailext attachLog: true, body: "构建URL:   ${env.BUILD_URL}\njenkins server 帐号密码:  请使用自己分配的账号密码登录\n构建序号:  ${env.BUILD_NUMBER}\n任务名称:  ${env.JOB_NAME}\n构建区间:  ${env.WORKSPACE}\n构建触发:  由用户${userName}触发\n控制台:  ${Url}\n控制台帐号密码:  xxxxxx/xxxxxx\nmonitor IP:  ${ipaddr}\nmonitor帐号密码:  xxxx/xxxxxxxx\n\n--------Testing Status--------\ntest_netwok构建结果:  ${test_network_status}\ntest_compute构建结果:  ${test_compute_status}\ntest_storage构建结果:  ${test_storage_status}\ntest_trove构建结果:  ${test_trove_status}\nEC_API_test构建结果:  ${test_ecs_status}\n\n--------Testing Urls--------\ntest_netwok构建Url:  ${test_network_url}\ntest_compute构建Url:  ${test_compute_url}\ntest_storage构建Url:  ${test_storage_url}\ntest_trove构建Url:  ${test_trove_url}\nEC_API_test构建Url:  ${test_ecs_url}", 
            replyTo: "${mailRecipients}", 
            subject: "[Jenkins] ${datePart} ${version} ${test_type} 每日构建${jobName}", 
            to: "${mailRecipients}"
        }   
      }   
    }
  } else {
    echo 'Skip'
  }
}
