---
title: GitOps - Structure
description: 
published: true
date: 2024-07-10T05:58:47.761Z
tags: 
editor: markdown
dateCreated: 2024-07-10T05:43:45.956Z
---

# GitOps - Structure

```

applications
	|__	manifests
	|	   |__ exertis-portal
	|	 		  |__production
	|	  		  |	  |__ main.yaml
	|	   		  |	  |__ manifest1
	|	   		  |	  |__ manifest2
	|	   		  |__staging
	|	   		  |	  |__ main.yaml
	|	  		  |	  |__ manifest1
	|	   		  |	  |__ manifest2
	|	    	  |__dev
	|	  		      |__ main.yaml
	|			      |__ manifest1
	|			      |__ manifest2
	|
	|
	|__	resources
		  |__ exertis-portal
		 		  |__ app1
		      |__ app2
		  |__ exertis-web
			      |__ app1
			      |__ app2


infratructure
	|__ manifests
	|	   |__ exertis-portal
	|	 	    	|__cluster-prod
 	|   	    	|     |__common
 	|   	    	|     |     |__ main.yaml # le manifest pointe vers "common dans ressources"
	|   	    	|     |__production
	|   	    	|           |__ main.yaml
	|   	    	| 	        |__ manifest1
	|   	    	|	        |__ manifest2
	|   	    	|__cluster-dev-staging
 	|   	    	      |__common
 	|   	    	      |     |__ main.yaml
	| 	        	 	  |__staging
	| 	        	      |     |__ main.yaml
	|   	    	 	  |	    |__ manifest1
	| 	        	 	  |	    |__ manifest2
	| 	        	      |__dev
	| 		                    |__ main.yaml
	| 		         		    |__ manifest1
	| 		         		    |__ manifest2
	|__resources
		   |__ exertis-portal
	  			    |__cluster-prod
	    		    |  	  |__common
	     	    	|     |     |__argo_ingres
	     	    	|	    |     |__argo_secerts
			      	|     |__ appinfra1
		 	      	|     |__ appinfra2
	  		      |__cluster-staging
	     		          |__common
	        		      |     |__argo_ingres
	        		      |     |__argo_secerts
					      |__ appinfra1
		 			      |__ appinfra2

```
