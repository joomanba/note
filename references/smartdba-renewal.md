## SSO Integration


**views.py**

```python

from django.conf import settings
...

redirect_uri = settings.SSO_REDIRECT_URI

def _query_list(db_nm, sql):

def check_if_user(user_id, user_pw):
	
	reuslt_auth = s.post('https://cloud-api.gsshop.com', headers=headers, data=json.dumps(body))

def get_user_img(sabun) 	

def login(request) :

	if div_dp == "DEV" :
		....
		
		if request.method == "POST" :
			if user is not None :
				...
			else:
				return render(request, 'login.html', {'error':'로그인 정보가 맞지 앖습니다.<br>홈넷 계정으로 로그인하세요.'})

		else :
			return render(request, 'login.html')

	else:
		
		code = request.GET.get('code')
		originUri = request.GET.get('next')

		if code is not None :
			...
		else :
			return redirect("https://login.gsretail.com/auth/realms/gsshop/protocol/openid-connect/auth?client_id=dataportal&response_type=code&redirect_uri={}".format(redirect_uri), code=302)
```