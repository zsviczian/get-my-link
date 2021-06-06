const cID = "a3e11dd0-bef0-444c-8224-e7a9346deaa4";

function launchOneDrivePicker(){
	var odOptions = { 
		clientId: cID,
		action: 'query',
		multiSelect: true,
		viewType: 'all',
		openInNewWindow: true,
		advanced: {
			redirectUri: 'https://get-my.link/redirect.html',
			scopes: 'Files.ReadWrite'
		},
		success: function(response) {
			oneDriveSuccess(response);//, get_thumbnail, public_url_request, thelink);
		},
		cancel: function() { /* cancel handler */ },
		error: function(error) { 
			errorMessage('Fetching file details failed: '+error);
			console.log(error);
		}
	};
	OneDrive.open(odOptions);
}


function launchSaveToOneDrive(type, url) {
	if (type == '') {
		alert('Select or drop a file first');
		return false;
	}
	
	var odOptions = {
		clientId: cID,
		action: 'save',
		sourceInputElementId: (type=='file') ? 'fileUploadControl' : '',
		sourceUri: (type=='url') ? url : '',
		fileName: $('input#uploadfilename').val(),
		openInNewWindow: true,
		nameConflictBehavior: 'rename', //fail, replace, fail
		advanced: {
			redirectUri: 'https://get-my.link/redirect.html',
			scopes: 'Files.ReadWrite'
		},
		success: function(response) { 
			oneDriveSuccess(response);
		},
		progress: function(percent) { 
		},
		cancel: function() { 
		},
		error: function(error) { 
			errorMessage('Save failed: '+error);
			console.log(error);
		}
	}
	OneDrive.save(odOptions);
}

function oneDriveSuccess(response) {
	$('legend#message').html('Working on your links...');

	$.each(response.value, function(index, value) {
		var url = response.apiEndpoint + 'drives/' + value.parentReference.driveId + '/items/' + value.id;
		$.ajax(
			{
				'url': url,
				beforeSend: function(request) {
					request.setRequestHeader('Authorization', 'Bearer ' + response.accessToken);
			},                            
			success: function(itemdata) {
				$('legend#message').html('Working on your links...');
				var url = response.apiEndpoint + 'drives/' + value.parentReference.driveId + '/items/' + value.id+'/createLink';
				$.ajax({
					'url': url,
					type: 'POST',
					data: JSON.stringify({
							type: "embed",
							scope: "anonymous"
					}),
					contentType: "application/json",
					dataType   : "json",
					beforeSend: function(request) {
						request.setRequestHeader("Authorization", 'Bearer ' + response.accessToken);
					},
					success: function(embeddata) {
						var url = response.apiEndpoint + 'shares/' + embeddata.shareId  + '/root/thumbnails';
						$.ajax({
							'url': url,
							type: 'GET',
							contentType: "application/json",
							dataType   : "json",
							beforeSend: function(request) {
								request.setRequestHeader("Authorization", 'Bearer ' + response.accessToken);
							},
							success: function(thumbdata) {
								set_fileFields(value,itemdata,embeddata,thumbdata);
							},
							error: function(err) {
								errorMessage('Fetching thumbnails failed: '+err);
								console.log(err);
							}
						});
					},
					error: function(err) {
						errorMessage('Fetching public_url failed: '+err);
						console.log(err);
					}
				});
			},
			error: function(error) {
				errorMessage('Fetching file details failed: '+error); 
			}
		});
	});
}
