<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Optical Shop Patient Records</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
            background-color: #f4f4f4;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        form {
            background-color: #fff;
            padding: 20px;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        label {
            display: block;
            margin-bottom: 5px;
        }
        input, select {
            width: 100%;
            padding: 8px;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        #searchForm {
            margin-bottom: 20px;
        }
        #patientList {
            margin-top: 20px;
        }
        .patient-card {
            background-color: #fff;
            border: 1px solid #ddd;
            border-radius: 4px;
            padding: 10px;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <h1>Optical Shop Patient Records</h1>
    
    <form id="searchForm">
        <input type="text" id="searchInput" placeholder="Search by name or phone number">
        <button type="submit">Search</button>
    </form>

    <form id="patientForm">
        <h2>Patient Information</h2>
        <label for="name">Name:</label>
        <input type="text" id="name" required>
        
        <label for="phone">Phone Number:</label>
        <input type="tel" id="phone" required>
        
        <label for="address">Address:</label>
        <input type="text" id="address" required>
        
        <label for="age">Age:</label>
        <input type="number" id="age" required>
        
        <label for="sex">Sex:</label>
        <select id="sex" required>
            <option value="male">Male</option>
            <option value="female">Female</option>
            <option value="other">Other</option>
        </select>
        
        <h2>Eye Power (After Refraction)</h2>
        <label for="rightSphere">Right Eye Sphere:</label>
        <input type="number" id="rightSphere" step="0.25" required>
        
        <label for="rightCylinder">Right Eye Cylinder:</label>
        <input type="number" id="rightCylinder" step="0.25" required>
        
        <label for="rightAxis">Right Eye Axis:</label>
        <input type="number" id="rightAxis" required>
        
        <label for="leftSphere">Left Eye Sphere:</label>
        <input type="number" id="leftSphere" step="0.25" required>
        
        <label for="leftCylinder">Left Eye Cylinder:</label>
        <input type="number" id="leftCylinder" step="0.25" required>
        
        <label for="leftAxis">Left Eye Axis:</label>
        <input type="number" id="leftAxis" required>
        
        <h2>Purchase Information</h2>
        <label for="frameType">Frame Type:</label>
        <input type="text" id="frameType">
        
        <label for="framePrice">Frame Price:</label>
        <input type="number" id="framePrice" step="0.01">
        
        <label for="lensType">Lens Type:</label>
        <input type="text" id="lensType">
        
        <label for="lensPrice">Lens Price:</label>
        <input type="number" id="lensPrice" step="0.01">
        
        <button type="submit">Save Patient Record</button>
    </form>

    <div id="patientList"></div>

    <script>
        // Load patients from local storage
        let patients = JSON.parse(localStorage.getItem('patients')) || [];

        const patientForm = document.getElementById('patientForm');
        const searchForm = document.getElementById('searchForm');
        const patientList = document.getElementById('patientList');

        // Save patient record
        patientForm.addEventListener('submit', function(e) {
            e.preventDefault();
            const patient = {
                id: Date.now(), // Unique ID for each patient
                name: document.getElementById('name').value,
                phone: document.getElementById('phone').value,
                address: document.getElementById('address').value,
                age: document.getElementById('age').value,
                sex: document.getElementById('sex').value,
                rightSphere: document.getElementById('rightSphere').value,
                rightCylinder: document.getElementById('rightCylinder').value,
                rightAxis: document.getElementById('rightAxis').value,
                leftSphere: document.getElementById('leftSphere').value,
                leftCylinder: document.getElementById('leftCylinder').value,
                leftAxis: document.getElementById('leftAxis').value,
                frameType: document.getElementById('frameType').value,
                framePrice: document.getElementById('framePrice').value,
                lensType: document.getElementById('lensType').value,
                lensPrice: document.getElementById('lensPrice').value
            };
            
            const existingPatientIndex = patients.findIndex(p => p.id === patient.id);
            if (existingPatientIndex !== -1) {
                patients[existingPatientIndex] = patient;
            } else {
                patients.push(patient);
            }
            
            localStorage.setItem('patients', JSON.stringify(patients));
            patientForm.reset();
            displayPatients();
        });

        // Search patients
        searchForm.addEventListener('submit', function(e) {
            e.preventDefault();
            const searchTerm = document.getElementById('searchInput').value.toLowerCase();
            const filteredPatients = patients.filter(patient => 
                patient.name.toLowerCase().includes(searchTerm) || 
                patient.phone.includes(searchTerm)
            );
            displayPatients(filteredPatients);
        });

        // Display patients
        function displayPatients(patientsToDisplay = patients) {
            patientList.innerHTML = '';
            patientsToDisplay.forEach(patient => {
                const patientCard = document.createElement('div');
                patientCard.className = 'patient-card';
                patientCard.innerHTML = `
                    <h3>${patient.name}</h3>
                    <p>Phone: ${patient.phone}</p>
                    <p>Address: ${patient.address}</p>
                    <p>Age: ${patient.age}</p>
                    <p>Sex: ${patient.sex}</p>
                    <p>Right Eye: ${patient.rightSphere}/${patient.rightCylinder}x${patient.rightAxis}</p>
                    <p>Left Eye: ${patient.leftSphere}/${patient.leftCylinder}x${patient.leftAxis}</p>
                    <p>Frame: ${patient.frameType} ($${patient.framePrice})</p>
                    <p>Lens: ${patient.lensType} ($${patient.lensPrice})</p>
                    <button onclick="editPatient(${patient.id})">Edit</button>
                `;
                patientList.appendChild(patientCard);
            });
        }

        // Edit patient
        function editPatient(id) {
            const patient = patients.find(p => p.id === id);
            if (patient) {
                document.getElementById('name').value = patient.name;
                document.getElementById('phone').value = patient.phone;
                document.getElementById('address').value = patient.address;
                document.getElementById('age').value = patient.age;
                document.getElementById('sex').value = patient.sex;
                document.getElementById('rightSphere').value = patient.rightSphere;
                document.getElementById('rightCylinder').value = patient.rightCylinder;
                document.getElementById('rightAxis').value = patient.rightAxis;
                document.getElementById('leftSphere').value = patient.leftSphere;
                document.getElementById('leftCylinder').value = patient.leftCylinder;
                document.getElementById('leftAxis').value = patient.leftAxis;
                document.getElementById('frameType').value = patient.frameType;
                document.getElementById('framePrice').value = patient.framePrice;
                document.getElementById('lensType').value = patient.lensType;
                document.getElementById('lensPrice').value = patient.lensPrice;
            }
        }

        // Initial display of patients
        displayPatients();
    </script>
</body>
</html>
