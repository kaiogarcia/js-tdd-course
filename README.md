# js-tdd-course
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, Image, TouchableOpacity } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import { StatusBar } from 'expo-status-bar';
import AsyncStorage from '@react-native-async-storage/async-storage';

const API_BASE_URL = 'https://api.github.com';

export default function App() {
  const [user, setUser] = useState(null);
  const [repositories, setRepositories] = useState([]);
  const [isFavorite, setIsFavorite] = useState(false);

  useEffect(() => {
    fetchUser();
    checkIfFavorite();
  }, []);

  const fetchUser = async () => {
    try {
      const response = await fetch(`${API_BASE_URL}/search/users?q=rgmellon`);
      const data = await response.json();

      if (data.items && data.items.length > 0) {
        const user = data.items[0];
        setUser(user);
        fetchRepositories(user.login);
      } else {
        // Define uma imagem de fallback se nenhum usuário for encontrado
        setUser({ avatar_url: 'https://github.githubassets.com/images/modules/logos_page/Octocat.png' });
      }
    } catch (error) {
      console.error('Erro ao buscar usuário:', error);
    }
  };

  const fetchRepositories = async (username) => {
    try {
      const response = await fetch(`${API_BASE_URL}/users/${username}/repos`);
      const repositories = await response.json();
      setRepositories(repositories);
    } catch (error) {
      console.error('Erro ao buscar repositórios:', error);
    }
  };

  const toggleFavorite = async () => {
    try {
      if (isFavorite) {
        await AsyncStorage.removeItem('favoriteUser');
      } else {
        const jsonValue = JSON.stringify(user);
        await AsyncStorage.setItem('favoriteUser', jsonValue);
      }

      setIsFavorite(!isFavorite);
    } catch (error) {
      console.error('Erro ao manipular favoritos:', error);
    }
  };

  const checkIfFavorite = async () => {
    try {
      const jsonValue = await AsyncStorage.getItem('favoriteUser');
      const favoriteUser = JSON.parse(jsonValue);
      setIsFavorite(!!favoriteUser);
    } catch (error) {
      console.error('Erro ao verificar favorito:', error);
    }
  };

  return (
    <View style={styles.container}>
      <View style={styles.header}>
        <Image source={{ uri: user ? user.avatar_url : '' }} style={styles.avatar} />
        <TouchableOpacity onPress={toggleFavorite}>
          <FontAwesome name={isFavorite ? 'star' : 'star-o'} size={24} color={styles.headerText.color} />
        </TouchableOpacity>
      </View>
      <View style={styles.tabBar}>
        {/* Adicione os ícones e funcionalidades da tabBar aqui */}
      </View>
      <View style={styles.content}>
        {user && (
          <View>
            <Text style={styles.repoTitle}>{user.login}</Text>
            <Text style={styles.repoDescription}>{user.bio}</Text>
          </View>
        )}
        <View>
          <Text style={styles.sectionTitle}>Repositórios:</Text>
          {repositories.map((repo) => (
            <View key={repo.id}>
              <Text style={styles.repoTitle}>{repo.name}</Text>
              <Text style={styles.repoDescription}>{repo.description}</Text>
            </View>
          ))}
        </View>
      </View>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  header: {
    backgroundColor: '#040404',
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    padding: 20,
  },
  avatar: {
    width: 50,
    height: 50,
    borderRadius: 25,
  },
  headerText: {
    color: '#FDFDFD',
    fontSize: 20,
    fontWeight: 'bold',
  },
  tabBar: {
    backgroundColor: '#F5F5F5',
    flexDirection: 'row',
    justifyContent: 'space-around',
    paddingVertical: 10,
  },
  content: {
    flex: 1,
    padding: 20,
  },
  sectionTitle: {
    color: '#040404',
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  repoTitle: {
    color: '#040404',
    fontSize: 18,
    fontWeight: 'bold',
  },
  repoDescription: {
    color: '#D6D6D6',
    fontSize: 14,
  },
});
